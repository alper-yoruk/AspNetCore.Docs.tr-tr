---
title: '8. bölüm, Razor ASP.NET Core EF Core olan sayfalar-eşzamanlılık'
author: rick-anderson
description: RazorSayfaların 8. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054001"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="9da17-103">8. bölüm, Razor ASP.NET Core EF Core olan sayfalar-eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="9da17-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="9da17-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)ve [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="9da17-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9da17-105">Bu öğreticide, birden çok kullanıcı bir varlığı eşzamanlı olarak (aynı zamanda) güncelleştirilişinde çakışmaların nasıl işleneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="9da17-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="9da17-106">Eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="9da17-106">Concurrency conflicts</span></span>

<span data-ttu-id="9da17-107">Şu durumlarda bir eşzamanlılık çakışması oluşur:</span><span class="sxs-lookup"><span data-stu-id="9da17-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="9da17-108">Bir Kullanıcı bir varlık için düzenleme sayfasına gider.</span><span class="sxs-lookup"><span data-stu-id="9da17-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="9da17-109">İlk kullanıcının değişikliği veritabanına yazılmadan önce, başka bir kullanıcı aynı varlığı güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="9da17-110">Eşzamanlılık algılama etkinleştirilmemişse, veritabanını güncelleştirme son olarak diğer kullanıcının değişikliklerinin üzerine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9da17-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="9da17-111">Bu risk kabul edilebilir ise, eşzamanlılık için programlama maliyeti avantaja aykırı bir ücret verebilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="9da17-112">Kötümser eşzamanlılık (kilitleme)</span><span class="sxs-lookup"><span data-stu-id="9da17-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="9da17-113">Eşzamanlılık çakışmalarını önlemenin bir yolu veritabanı kilitlerini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9da17-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="9da17-114">Bu, Kötümser eşzamanlılık olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9da17-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="9da17-115">Uygulama, güncelleştirmeyi amaçladığı bir veritabanı satırını okumadan önce bir kilit ister.</span><span class="sxs-lookup"><span data-stu-id="9da17-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="9da17-116">Bir satır, güncelleştirme erişimi için kilitlendiğinde, ilk kilit yayımlanıncaya kadar başka hiçbir kullanıcının satırı kilitlemesine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="9da17-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="9da17-117">Kilitleri yönetmek dezavantajlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9da17-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="9da17-118">Program, karmaşık olabilir ve Kullanıcı sayısı arttıkça performans sorunlarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="9da17-119">Entity Framework Core, BT için yerleşik destek sağlamaz ve bu öğretici Bu öğreticinin nasıl uygulanacağını göstermez.</span><span class="sxs-lookup"><span data-stu-id="9da17-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="9da17-120">İyimser eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="9da17-120">Optimistic concurrency</span></span>

<span data-ttu-id="9da17-121">İyimser eşzamanlılık eşzamanlılık çakışmalarının gerçekleşmesini sağlar ve ardından, ne zaman uygun şekilde yeniden çalışır.</span><span class="sxs-lookup"><span data-stu-id="9da17-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="9da17-122">Örneğin, Gamze departman düzenleme sayfasını ziyaret ettiğinde, Ingilizce bölümünün bütçesini $350.000,00 ' den $0,00 ' e değiştiriyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget30.png)

<span data-ttu-id="9da17-124">Kemal, **Kaydet** ' i tıklamadan önce, John aynı sayfayı ziyaret ettiğinde başlangıç tarihi alanını 9/1/2007 ' den 9/1/2013 ' e değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-124">Before Jane clicks **Save** , John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date30.png)

<span data-ttu-id="9da17-126">Gamze önce **Kaydet** ' i tıklatır ve değişiklik etkin duruma geçer çünkü tarayıcı Dizin sayfasını bütçe miktarı olarak sıfır ile görüntülüyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="9da17-127">John, hala $350.000,00 bütçesini gösteren bir düzenleme sayfasında **Kaydet** ' i tıklatır.</span><span class="sxs-lookup"><span data-stu-id="9da17-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="9da17-128">Sonraki durum eşzamanlılık çakışmalarını nasıl işleydiğinize göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="9da17-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="9da17-129">Bir kullanıcının hangi özelliği değiştirdiği ve yalnızca ilgili sütunları veritabanında güncelleştirdiğinden haberdar olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="9da17-130">Senaryosunda hiçbir veri kaybolmaz.</span><span class="sxs-lookup"><span data-stu-id="9da17-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="9da17-131">Farklı özellikler iki kullanıcı tarafından güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="9da17-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="9da17-132">Ingilizce bölüme bir dahaki sefer gözattığında, hem gamze 'nin hem de John 'un değişikliklerini görürler.</span><span class="sxs-lookup"><span data-stu-id="9da17-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="9da17-133">Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışmaların sayısını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="9da17-134">Bu yaklaşım bazı dezavantajlara sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9da17-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="9da17-135">Aynı özellikte rekabet değişiklikleri yapılmışsa veri kaybını önleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="9da17-136">Genellikle bir Web uygulamasında pratik değildir.</span><span class="sxs-lookup"><span data-stu-id="9da17-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="9da17-137">Tüm getirilen değerleri ve yeni değerleri izlemek için önemli durumun sürdürülmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="9da17-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="9da17-138">Büyük miktarlarda durum bulundurma, uygulama performansını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="9da17-139">, Bir varlıkta eşzamanlılık algılama ile karşılaştırıldığında uygulama karmaşıklığını artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="9da17-140">John 'un değişikliğini kemal 'in değişikliğini geçersiz kılabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="9da17-141">Ingilizce bölüme bir dahaki sefer gözattığında, 9/1/2013 ve getirilen $350.000,00 değerini görür.</span><span class="sxs-lookup"><span data-stu-id="9da17-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="9da17-142">Bu yaklaşım, *Istemci WINS* veya *son WINS* senaryosu olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9da17-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="9da17-143">(İstemciden gelen tüm değerler veri deposunda yer alacak şekilde önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, Istemci WINS otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="9da17-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="9da17-144">John 'un değişikliğini veritabanında güncelleştirilmesini engelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="9da17-145">Uygulama genellikle şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="9da17-145">Typically, the app would:</span></span>

  * <span data-ttu-id="9da17-146">Bir hata iletisi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-146">Display an error message.</span></span>
  * <span data-ttu-id="9da17-147">Verilerin geçerli durumunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="9da17-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="9da17-148">Kullanıcının değişiklikleri yeniden uygulamasına izin verin.</span><span class="sxs-lookup"><span data-stu-id="9da17-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="9da17-149">Buna *Mağaza WINS* senaryosu denir.</span><span class="sxs-lookup"><span data-stu-id="9da17-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="9da17-150">(Veri deposu değerleri, istemci tarafından gönderilen değerlere göre önceliklidir.) Bu öğreticide mağaza WINS senaryosunu uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="9da17-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="9da17-151">Bu yöntem, bir Kullanıcı uyarı vermeden hiçbir değişikliğin üzerine yazılmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="9da17-152">EF Core çakışma algılama</span><span class="sxs-lookup"><span data-stu-id="9da17-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="9da17-153">EF Core, `DbConcurrencyException` Çakışmalar algıladığında özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9da17-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="9da17-154">Çakışma algılamayı etkinleştirmek için veri modeli yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9da17-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="9da17-155">Çakışma algılamayı etkinleştirme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="9da17-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="9da17-156">EF Core, Update ve DELETE komutlarının WHERE yan tümcesinde [eşzamanlılık belirteçleri](/ef/core/modeling/concurrency) olarak yapılandırılan sütunların özgün değerlerini içerecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="9da17-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="9da17-157">`SaveChanges`Çağrıldığında WHERE yan tümcesi, özniteliğiyle birlikte açıklanan özelliklerin özgün değerlerini arar <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> .</span><span class="sxs-lookup"><span data-stu-id="9da17-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="9da17-158">Update deyimleri, satır ilk okunduğundan bu yana eşzamanlılık belirteci özelliklerinden herhangi biri değiştiyse güncelleştirilecek bir satır bulmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="9da17-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="9da17-159">EF Core eşzamanlılık çakışması olarak yorumlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="9da17-160">Birçok sütunu olan veritabanı tablolarında bu yaklaşım çok büyük WHERE yan tümceleriyle sonuçlanabilir ve büyük miktarlarda durum gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="9da17-161">Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.</span><span class="sxs-lookup"><span data-stu-id="9da17-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="9da17-162">Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemede kullanılabilecek bir izleme sütunu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9da17-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="9da17-163">SQL Server veritabanında, izleme sütununun veri türü olur `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="9da17-164">`rowversion`Değer, satır her güncelleştirildiği zaman artılan sıralı bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9da17-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="9da17-165">Bir Update veya delete komutunda WHERE yan tümcesi, izleme sütununun (orijinal satır sürüm numarası) orijinal değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="9da17-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="9da17-166">Güncelleştirilmekte olan satır başka bir kullanıcı tarafından değiştirilmişse `rowversion` sütunundaki değer özgün değerden farklı olur.</span><span class="sxs-lookup"><span data-stu-id="9da17-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="9da17-167">Bu durumda, Update veya DELETE deyimi WHERE yan tümcesi nedeniyle güncelleştirilecek satırı bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="9da17-168">Bir Update veya delete komutundan hiçbir satır etkilenmeden EF Core eşzamanlılık özel durumu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9da17-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="9da17-169">İzleme özelliği Ekle</span><span class="sxs-lookup"><span data-stu-id="9da17-169">Add a tracking property</span></span>

<span data-ttu-id="9da17-170">*Modeller/departman. cs* ' de, rowversion adlı bir izleme özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9da17-170">In *Models/Department.cs* , add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="9da17-171"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute>Özniteliği sütunu eşzamanlılık izleme sütunu olarak tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="9da17-172">Fluent API, izleme özelliğini belirtmenin alternatif bir yoludur:</span><span class="sxs-lookup"><span data-stu-id="9da17-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="9da17-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9da17-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9da17-174">Bir SQL Server veritabanı için, bir `[Timestamp]` varlık özelliği üzerindeki özniteliği byte dizisi olarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="9da17-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="9da17-175">Sütunun WHERE yan tümceleri DELETE ve UPDATE 'e dahil edilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="9da17-176">Veritabanındaki sütun türünü [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="9da17-177">Veritabanı, satır her güncelleştirildiği zaman artılan sıralı bir satır sürüm numarası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9da17-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="9da17-178">`Update`Veya `Delete` komutunda, `Where` yan tümce getirilen satır sürümü değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="9da17-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="9da17-179">Güncelleştirilmekte olan satır getirilmesinden sonra değiştiyse:</span><span class="sxs-lookup"><span data-stu-id="9da17-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="9da17-180">Geçerli satır sürümü değeri getirilen değerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="9da17-181">`Update` `Delete` `Where` Yan tümce getirilen satır sürümü değerini aradığı için veya komutları bir satır bulmayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="9da17-182">Bir `DbUpdateConcurrencyException` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="9da17-183">Aşağıdaki kod, Bölüm adı güncelleştirildiği zaman EF Core tarafından oluşturulan T-SQL ' in bir bölümünü gösterir:</span><span class="sxs-lookup"><span data-stu-id="9da17-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="9da17-184">Önceki vurgulanan kod, `WHERE` içeren yan tümceyi gösterir `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="9da17-185">Veritabanı `RowVersion` `RowVersion` () parametresine eşit değilse `@p2` , hiçbir satır güncellenmez.</span><span class="sxs-lookup"><span data-stu-id="9da17-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="9da17-186">Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirildiğini doğrulayan T-SQL ' i göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="9da17-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="9da17-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Son deyimden etkilenen satır sayısını döndürür.</span><span class="sxs-lookup"><span data-stu-id="9da17-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="9da17-188">Hiçbir satır güncellenmemişse EF Core bir oluşturur `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="9da17-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9da17-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9da17-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9da17-190">Bir SQLite veritabanı için, `[Timestamp]` bir varlık özelliği üzerindeki özniteliği byte dizisi olarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="9da17-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="9da17-191">Sütunun WHERE yan tümceleri DELETE ve UPDATE 'e dahil edilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="9da17-192">BLOB sütun türüyle eşlenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="9da17-193">Veritabanı Tetikleyicileri satır her güncelleştirildiğinde, RowVersion sütununu yeni bir rastgele bayt dizisiyle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="9da17-194">`Update`Or `Delete` komutunda, `Where` yan tümce rowversion sütununun getirilen değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="9da17-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="9da17-195">Güncelleştirilmekte olan satır getirilmesinden sonra değiştiyse:</span><span class="sxs-lookup"><span data-stu-id="9da17-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="9da17-196">Geçerli satır sürümü değeri getirilen değerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="9da17-197">`Update`Or `Delete` komutu bir satır bulmadığından, `Where` yan tümce orijinal satır sürümü değerini arar.</span><span class="sxs-lookup"><span data-stu-id="9da17-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="9da17-198">Bir `DbUpdateConcurrencyException` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="9da17-199">Veritabanını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="9da17-199">Update the database</span></span>

<span data-ttu-id="9da17-200">Özelliği eklendiğinde `RowVersion` geçiş gerektiren veri modeli değişir.</span><span class="sxs-lookup"><span data-stu-id="9da17-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="9da17-201">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="9da17-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="9da17-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9da17-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9da17-203">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9da17-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9da17-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9da17-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9da17-205">Bir terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9da17-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="9da17-206">Şu komut:</span><span class="sxs-lookup"><span data-stu-id="9da17-206">This command:</span></span>

* <span data-ttu-id="9da17-207">*Geçişleri/{zaman damgası} _RowVersion. cs* geçiş dosyasını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9da17-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="9da17-208">*Geçişler/SchoolContextModelSnapshot. cs* dosyasını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="9da17-209">Bu güncelleştirme yöntemine aşağıdaki vurgulanmış kodu ekler `BuildModel` :</span><span class="sxs-lookup"><span data-stu-id="9da17-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="9da17-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9da17-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9da17-211">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9da17-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9da17-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9da17-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9da17-213">Dosyayı açın `Migrations/<timestamp>_RowVersion.cs` ve vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9da17-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="9da17-214">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="9da17-214">The preceding code:</span></span>

  * <span data-ttu-id="9da17-215">Mevcut satırları rastgele blob değerleriyle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="9da17-216">Satır her güncelleştirildiğinde ROWVERSION sütununu rastgele bir blob değerine ayarlamış veritabanı Tetikleyicileri ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="9da17-217">Bir terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9da17-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="9da17-218">Yapı iskelesi departmanı sayfaları</span><span class="sxs-lookup"><span data-stu-id="9da17-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9da17-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9da17-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9da17-220">Aşağıdaki özel durumlarla birlikte [Yapı Fkatlama öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="9da17-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="9da17-221">*Sayfalar/departmanlar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9da17-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="9da17-222">`Department`Model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9da17-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="9da17-223">Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="9da17-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9da17-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9da17-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9da17-225">*Sayfalar/departmanlar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9da17-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="9da17-226">Bölüm sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9da17-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="9da17-227">**Windows 'da:**</span><span class="sxs-lookup"><span data-stu-id="9da17-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="9da17-228">**Linux veya macOS 'ta:**</span><span class="sxs-lookup"><span data-stu-id="9da17-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="9da17-229">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="9da17-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="9da17-230">Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-230">Update the Index page</span></span>

<span data-ttu-id="9da17-231">Scafkatlama aracı `RowVersion` Dizin sayfası için bir sütun oluşturdu, ancak bu alan bir üretim uygulamasında gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="9da17-232">Bu öğreticide, `RowVersion` eşzamanlılık işlemenin nasıl çalıştığını göstermeye yardımcı olmak için öğesinin son baytı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="9da17-233">Son baytın kendi kendine benzersiz olması garanti edilmez.</span><span class="sxs-lookup"><span data-stu-id="9da17-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="9da17-234">*Pages\Departments\Index.cshtml* sayfasını güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="9da17-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="9da17-235">Dizini departmanlar ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9da17-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="9da17-236">`RowVersion`Yalnızca bayt dizisinin son baytını göstermek için içeren kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9da17-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="9da17-237">FirstMidName öğesini FullName ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9da17-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="9da17-238">Aşağıdaki kod, güncelleştirilmiş sayfayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="9da17-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="9da17-239">Düzenleme sayfası modelini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="9da17-239">Update the Edit page model</span></span>

<span data-ttu-id="9da17-240">Aşağıdaki kodla *Pages\Departments\Edit.cshtml.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="9da17-241">, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> `rowVersion` Yönteminde alındığı sırada varlıktaki değerle güncelleştirilir `OnGet` .</span><span class="sxs-lookup"><span data-stu-id="9da17-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="9da17-242">EF Core, özgün değeri içeren WHERE yan tümcesiyle bir SQL UPDATE komutu oluşturur `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="9da17-243">UPDATE komutundan hiçbir satır etkilenmeden (hiçbir satır özgün değere sahip değilse `RowVersion` ), bir `DbUpdateConcurrencyException` özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="9da17-244">Vurgulanan kodda:</span><span class="sxs-lookup"><span data-stu-id="9da17-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="9da17-245">İçindeki değeri, `Department.RowVersion` ilk olarak düzenleme sayfasına yönelik GET isteğine getirilen varlıkta olduğu şeydir.</span><span class="sxs-lookup"><span data-stu-id="9da17-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="9da17-246">Değer, `OnPost` Razor düzenlenecek varlığı görüntüleyen sayfadaki bir gizli alanla yöntemine sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9da17-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="9da17-247">Gizli alan değeri `Department.RowVersion` model cildi tarafından öğesine kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="9da17-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="9da17-248">`OriginalValue` WHERE yan tümcesinde EF Core kullanılacak şeydir.</span><span class="sxs-lookup"><span data-stu-id="9da17-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="9da17-249">Vurgulanan kod satırından önce, `OriginalValue` Bu yöntemde çağrıldığında veritabanında bulunan değeri, `FirstOrDefaultAsync` düzenleme sayfasında görüntülendiklerden farklı olabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="9da17-250">Vurgulanan kod, EF Core `RowVersion` `Department` SQL Update ifadesinin WHERE yan tümcesinde görüntülenen varlıktaki özgün değeri kullandığından emin olur.</span><span class="sxs-lookup"><span data-stu-id="9da17-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="9da17-251">Bir eşzamanlılık hatası oluştuğunda, aşağıdaki vurgulanmış kod istemci değerlerini (Bu yönteme gönderilen değerler) ve veritabanı değerlerini alır.</span><span class="sxs-lookup"><span data-stu-id="9da17-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="9da17-252">Aşağıdaki kod, ' a gönderilen değerden farklı veritabanı değerleri olan her bir sütun için özel bir hata iletisi ekler `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="9da17-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="9da17-253">Aşağıdaki vurgulanan kod, `RowVersion` değeri veritabanından alınan yeni değer olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="9da17-254">Kullanıcının bir dahaki sefer **Kaydet** ' i tıklatması durumunda, yalnızca düzenleme sayfasının son görüntüsü bu yana gerçekleşen eşzamanlılık hataları yakalanacaktır.</span><span class="sxs-lookup"><span data-stu-id="9da17-254">The next time the user clicks **Save** , only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="9da17-255">`ModelState.Remove`Eski değere sahip olduğundan deyimin olması gerekir `ModelState` `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="9da17-256">RazorSayfada, `ModelState` bir alanın değeri, her ikisi de varsa Model Özellik değerlerinden önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="9da17-257">Düzenleme sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-257">Update the Edit page</span></span>

<span data-ttu-id="9da17-258">*Sayfaları/departmanları/Düzenle. cshtml* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="9da17-259">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="9da17-259">The preceding code:</span></span>

* <span data-ttu-id="9da17-260">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="9da17-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9da17-261">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-261">Adds a hidden row version.</span></span> <span data-ttu-id="9da17-262">`RowVersion` , geri gönderme, değeri bağlayan şekilde eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="9da17-263">`RowVersion`Hata ayıklama amacıyla son baytını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="9da17-264">`ViewData`Türü kesin belirlenmiş olan ile değiştirilir `InstructorNameSL` .</span><span class="sxs-lookup"><span data-stu-id="9da17-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="9da17-265">Düzenleme sayfasıyla eşzamanlılık çakışmalarını test etme</span><span class="sxs-lookup"><span data-stu-id="9da17-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="9da17-266">Ingilizce bölümünde düzenleme öğesinin iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="9da17-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="9da17-267">Uygulamayı çalıştırın ve Departmanlar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="9da17-268">Ingilizce departman için **düzenleme** köprüsüne sağ tıklayın ve **Yeni sekmede aç** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab** .</span></span>
* <span data-ttu-id="9da17-269">İlk sekmede, Ingilizce bölümünün **düzenleme** Köprüsü ' ne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="9da17-270">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9da17-271">İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-271">Change the name in the first browser tab and click **Save** .</span></span>

![Bölüm Düzenle sayfa 1 değişiklikten sonra](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="9da17-273">Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9da17-274">Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="9da17-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9da17-275">İkinci tarayıcı sekmesinden farklı bir alanı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9da17-275">Change a different field in the second browser tab.</span></span>

![Değişiklik sonrasında bölüm düzenleme sayfası 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="9da17-277">**Kaydet** ’e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-277">Click **Save** .</span></span> <span data-ttu-id="9da17-278">Veritabanı değerleriyle eşleşmeyen tüm alanlar için hata iletileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="9da17-278">You see error messages for all fields that don't match the database values:</span></span>

![Bölüm düzenleme sayfası hata iletisi](concurrency/_static/edit-error30.png)

<span data-ttu-id="9da17-280">Bu tarayıcı penceresi, ad alanını değiştirmeyi planlamadı.</span><span class="sxs-lookup"><span data-stu-id="9da17-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="9da17-281">Geçerli değeri (diller) kopyalayın ve ad alanına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="9da17-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="9da17-282">Sekme genişletme. İstemci tarafı doğrulaması hata iletisini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9da17-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="9da17-283">Yeniden **Kaydet** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-283">Click **Save** again.</span></span> <span data-ttu-id="9da17-284">İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="9da17-285">Kaydedilen değerleri Dizin sayfasında görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="9da17-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="9da17-286">Sayfa silme modelini Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-286">Update the Delete page model</span></span>

<span data-ttu-id="9da17-287">*Sayfaları/departmanları/delete. cshtml. cs* öğesini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="9da17-288">Sil sayfası, varlık alındıktan sonra değiştirildiğinde eşzamanlılık çakışmalarını algılar.</span><span class="sxs-lookup"><span data-stu-id="9da17-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="9da17-289">`Department.RowVersion` , varlık getirilen satır sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="9da17-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="9da17-290">EF Core, SQL DELETE komutunu oluşturduğunda, içeren bir WHERE yan tümcesi içerir `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="9da17-291">SQL DELETE komutu sıfır satırla etkileniyorsa:</span><span class="sxs-lookup"><span data-stu-id="9da17-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="9da17-292">`RowVersion`SQL DELETE komutunda bulunan, `RowVersion` veritabanında eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="9da17-293">Bir DbUpdateConcurrencyException özel durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="9da17-294">`OnGetAsync` ile çağırılır `concurrencyError` .</span><span class="sxs-lookup"><span data-stu-id="9da17-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="9da17-295">Silme sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-295">Update the Delete page</span></span>

<span data-ttu-id="9da17-296">*Sayfaları/departmanları/delete. cshtml* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="9da17-297">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="9da17-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="9da17-298">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="9da17-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9da17-299">Bir hata iletisi ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-299">Adds an error message.</span></span>
* <span data-ttu-id="9da17-300">FirstMidName öğesini, **yönetici** alanındaki FullName ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="9da17-301">`RowVersion`Son baytın görüntüleneceği değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="9da17-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="9da17-302">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-302">Adds a hidden row version.</span></span> <span data-ttu-id="9da17-303">`RowVersion` , geri gönderme, değeri bağlayan şekilde eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="9da17-304">Eşzamanlılık çakışmalarını test et</span><span class="sxs-lookup"><span data-stu-id="9da17-304">Test concurrency conflicts</span></span>

<span data-ttu-id="9da17-305">Test departmanı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9da17-305">Create a test department.</span></span>

<span data-ttu-id="9da17-306">Test departmanı üzerinde silmenin iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="9da17-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="9da17-307">Uygulamayı çalıştırın ve Departmanlar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="9da17-308">Test departmanı için **silme** köprüsünü sağ tıklayın ve **Yeni sekmede aç** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab** .</span></span>
* <span data-ttu-id="9da17-309">Test departmanı için **düzenleme** köprüsüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="9da17-310">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9da17-311">İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-311">Change the budget in the first browser tab and click **Save** .</span></span>

<span data-ttu-id="9da17-312">Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9da17-313">Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="9da17-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9da17-314">İkinci sekmeden test departmanını silin. Veritabanında geçerli değerlerle birlikte bir eşzamanlılık hatası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="9da17-315">**Sil** ' i tıklatmak, güncelleştirilmemiş olmadığı takdirde varlığı siler `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9da17-316">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9da17-316">Additional resources</span></span>

* [<span data-ttu-id="9da17-317">EF Core eşzamanlılık belirteçleri</span><span class="sxs-lookup"><span data-stu-id="9da17-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="9da17-318">EF Core eşzamanlılık işle</span><span class="sxs-lookup"><span data-stu-id="9da17-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="9da17-319">2. x kaynağı ASP.NET Core hata ayıklaması</span><span class="sxs-lookup"><span data-stu-id="9da17-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="9da17-320">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="9da17-320">Next steps</span></span>

<span data-ttu-id="9da17-321">Bu, serideki son öğreticidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="9da17-322">[Bu öğretici serisinin MVC sürümünde](xref:data/ef-mvc/index)ek konular ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9da17-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9da17-323">Önceki öğretici</span><span class="sxs-lookup"><span data-stu-id="9da17-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9da17-324">Bu öğreticide, birden çok kullanıcı bir varlığı eşzamanlı olarak (aynı zamanda) güncelleştirilişinde çakışmaların nasıl işleneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="9da17-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="9da17-325">Sorun yaşıyorsanız, bu [uygulamayı indiremez veya görüntüleyemezsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="9da17-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="9da17-326">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="9da17-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="9da17-327">Eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="9da17-327">Concurrency conflicts</span></span>

<span data-ttu-id="9da17-328">Şu durumlarda bir eşzamanlılık çakışması oluşur:</span><span class="sxs-lookup"><span data-stu-id="9da17-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="9da17-329">Bir Kullanıcı bir varlık için düzenleme sayfasına gider.</span><span class="sxs-lookup"><span data-stu-id="9da17-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="9da17-330">İlk kullanıcının değişikliği VERITABANıNA yazılmadan önce, başka bir kullanıcı aynı varlığı güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="9da17-331">Eşzamanlılık algılama etkin değilse, eşzamanlı güncelleştirmeler gerçekleştiğinde:</span><span class="sxs-lookup"><span data-stu-id="9da17-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="9da17-332">Son güncelleştirme kazanır.</span><span class="sxs-lookup"><span data-stu-id="9da17-332">The last update wins.</span></span> <span data-ttu-id="9da17-333">Diğer bir deyişle, son güncelleştirme değerleri VERITABANıNA kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="9da17-334">Geçerli güncelleştirmelerin ilki kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="9da17-335">İyimser eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="9da17-335">Optimistic concurrency</span></span>

<span data-ttu-id="9da17-336">İyimser eşzamanlılık eşzamanlılık çakışmalarının gerçekleşmesini sağlar ve ardından, ne zaman uygun şekilde yeniden çalışır.</span><span class="sxs-lookup"><span data-stu-id="9da17-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="9da17-337">Örneğin, Gamze departman düzenleme sayfasını ziyaret ettiğinde, Ingilizce bölümünün bütçesini $350.000,00 ' den $0,00 ' e değiştiriyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget.png)

<span data-ttu-id="9da17-339">Kemal, **Kaydet** ' i tıklamadan önce, John aynı sayfayı ziyaret ettiğinde başlangıç tarihi alanını 9/1/2007 ' den 9/1/2013 ' e değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-339">Before Jane clicks **Save** , John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date.png)

<span data-ttu-id="9da17-341">Gamze önce **Kaydet** ' i tıklatır ve tarayıcı Dizin sayfasını görüntülediğinde değişikliği görür.</span><span class="sxs-lookup"><span data-stu-id="9da17-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Bütçe sıfıra değişti](concurrency/_static/budget-zero.png)

<span data-ttu-id="9da17-343">John, hala $350.000,00 bütçesini gösteren bir düzenleme sayfasında **Kaydet** ' i tıklatır.</span><span class="sxs-lookup"><span data-stu-id="9da17-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="9da17-344">Sonraki durum eşzamanlılık çakışmalarını nasıl işleydiğinize göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="9da17-345">İyimser eşzamanlılık aşağıdaki seçenekleri içerir:</span><span class="sxs-lookup"><span data-stu-id="9da17-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="9da17-346">Bir kullanıcının hangi özelliği değiştirmiş olduğunu izleyebilir ve yalnızca VERITABANıNDAKI ilgili sütunları güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="9da17-347">Senaryosunda hiçbir veri kaybolmaz.</span><span class="sxs-lookup"><span data-stu-id="9da17-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="9da17-348">Farklı özellikler iki kullanıcı tarafından güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="9da17-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="9da17-349">Ingilizce bölüme bir dahaki sefer gözattığında, hem gamze 'nin hem de John 'un değişikliklerini görürler.</span><span class="sxs-lookup"><span data-stu-id="9da17-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="9da17-350">Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışmaların sayısını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="9da17-351">Bu yaklaşım:</span><span class="sxs-lookup"><span data-stu-id="9da17-351">This approach:</span></span>
 
  * <span data-ttu-id="9da17-352">Aynı özellikte rekabet değişiklikleri yapılmışsa veri kaybını önleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="9da17-353">Genellikle bir Web uygulamasında pratik değildir.</span><span class="sxs-lookup"><span data-stu-id="9da17-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="9da17-354">Tüm getirilen değerleri ve yeni değerleri izlemek için önemli durumun sürdürülmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="9da17-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="9da17-355">Büyük miktarlarda durum bulundurma, uygulama performansını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="9da17-356">, Bir varlıkta eşzamanlılık algılama ile karşılaştırıldığında uygulama karmaşıklığını artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="9da17-357">John 'un değişikliğini kemal 'in değişikliğini geçersiz kılabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="9da17-358">Ingilizce bölüme bir dahaki sefer gözattığında, 9/1/2013 ve getirilen $350.000,00 değerini görür.</span><span class="sxs-lookup"><span data-stu-id="9da17-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="9da17-359">Bu yaklaşım, *Istemci WINS* veya *son WINS* senaryosu olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9da17-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="9da17-360">(İstemciden gelen tüm değerler veri deposunda yer alacak şekilde önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, Istemci WINS otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="9da17-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="9da17-361">John 'un değişikliğini VERITABANıNDA güncelleştirilmesini engelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="9da17-362">Uygulama genellikle şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="9da17-362">Typically, the app would:</span></span>

  * <span data-ttu-id="9da17-363">Bir hata iletisi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-363">Display an error message.</span></span>
  * <span data-ttu-id="9da17-364">Verilerin geçerli durumunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="9da17-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="9da17-365">Kullanıcının değişiklikleri yeniden uygulamasına izin verin.</span><span class="sxs-lookup"><span data-stu-id="9da17-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="9da17-366">Buna *Mağaza WINS* senaryosu denir.</span><span class="sxs-lookup"><span data-stu-id="9da17-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="9da17-367">(Veri deposu değerleri, istemci tarafından gönderilen değerlere göre önceliklidir.) Bu öğreticide mağaza WINS senaryosunu uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="9da17-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="9da17-368">Bu yöntem, bir Kullanıcı uyarı vermeden hiçbir değişikliğin üzerine yazılmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="9da17-369">Eşzamanlılık işleme</span><span class="sxs-lookup"><span data-stu-id="9da17-369">Handling concurrency</span></span> 

<span data-ttu-id="9da17-370">Bir özellik [eşzamanlılık belirteci](/ef/core/modeling/concurrency)olarak yapılandırıldığında:</span><span class="sxs-lookup"><span data-stu-id="9da17-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="9da17-371">EF Core, özelliğin getirildikten sonra değiştirilmediğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="9da17-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="9da17-372">Bu denetim, [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) veya [Savechangesasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="9da17-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="9da17-373">Özellik alındıktan sonra değiştirilmişse, bir [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="9da17-374">DB ve veri modeli, üretilmesini destekleyecek şekilde yapılandırılmalıdır `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="9da17-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="9da17-375">Bir özellikte eşzamanlılık çakışmalarını algılama</span><span class="sxs-lookup"><span data-stu-id="9da17-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="9da17-376">Eşzamanlılık çakışmaları özellik düzeyinde [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) özniteliğiyle algılanabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="9da17-377">Öznitelik, modeldeki birden çok özelliğe uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="9da17-378">Daha fazla bilgi için bkz. [veri ek açıklamaları-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="9da17-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="9da17-379">`[ConcurrencyCheck]`Öznitelik bu öğreticide kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9da17-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="9da17-380">Bir satırda eşzamanlılık çakışmalarını algılama</span><span class="sxs-lookup"><span data-stu-id="9da17-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="9da17-381">Eşzamanlılık çakışmalarını algılamak için, modele bir [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql) izleme sütunu eklenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="9da17-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="9da17-382">`rowversion` :</span></span>

* <span data-ttu-id="9da17-383">SQL Server özeldir.</span><span class="sxs-lookup"><span data-stu-id="9da17-383">Is SQL Server specific.</span></span> <span data-ttu-id="9da17-384">Diğer veritabanları benzer bir özellik sağlayamayabilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="9da17-385">, DB 'den getirilmesinden sonra bir varlığın değiştirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9da17-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="9da17-386">DB, `rowversion` satır her güncelleştirildiği zaman arttırılan sıralı bir sayı üretir.</span><span class="sxs-lookup"><span data-stu-id="9da17-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="9da17-387">`Update`Or `Delete` komutunda, `Where` yan tümcesi getirilen değerini içerir `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="9da17-388">Güncelleştirilmekte olan satır değiştiyse:</span><span class="sxs-lookup"><span data-stu-id="9da17-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="9da17-389">`rowversion` getirilen değerle eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="9da17-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="9da17-390">`Update`Yan tümcesi getirilen ' i içerdiğinden veya `Delete` komutları bir satır bulmayın `Where` `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="9da17-391">Bir `DbUpdateConcurrencyException` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="9da17-392">EF Core, bir veya komutu tarafından hiçbir satır güncellenmemişse `Update` `Delete` , bir eşzamanlılık özel durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="9da17-393">Bölüm varlığına bir izleme özelliği ekleyin</span><span class="sxs-lookup"><span data-stu-id="9da17-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="9da17-394">*Modeller/departman. cs* ' de, rowversion adlı bir izleme özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9da17-394">In *Models/Department.cs* , add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="9da17-395">[Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) özniteliği, bu sütunun `Where` yan tümcesine `Update` ve komutlarına dahil edildiğini belirtir `Delete` .</span><span class="sxs-lookup"><span data-stu-id="9da17-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="9da17-396">Özniteliği, `Timestamp` SQL Server önceki SÜRÜMLERI `timestamp` SQL türü DEĞIŞTIRILMEDEN önce SQL veri türü kullandığından çağrılır `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="9da17-397">Fluent API izleme özelliğini de belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="9da17-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="9da17-398">Aşağıdaki kod, Bölüm adı güncelleştirildiği zaman EF Core tarafından oluşturulan T-SQL ' in bir bölümünü gösterir:</span><span class="sxs-lookup"><span data-stu-id="9da17-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="9da17-399">Önceki vurgulanan kod, `WHERE` içeren yan tümceyi gösterir `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="9da17-400">DB `RowVersion` `RowVersion` parametresi () değerine eşit değilse `@p2` , hiçbir satır güncellenmez.</span><span class="sxs-lookup"><span data-stu-id="9da17-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="9da17-401">Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirildiğini doğrulayan T-SQL ' i göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="9da17-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="9da17-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Son deyimden etkilenen satır sayısını döndürür.</span><span class="sxs-lookup"><span data-stu-id="9da17-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="9da17-403">Hiçbir satır güncelleştirilmemiş EF Core bir oluşturur `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="9da17-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="9da17-404">T-SQL EF Core, Visual Studio 'nun çıkış penceresinde görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9da17-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="9da17-405">DB 'yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="9da17-405">Update the DB</span></span>

<span data-ttu-id="9da17-406">Özelliği eklendiğinde `RowVersion` , geçiş GEREKTIREN DB modeli değişir.</span><span class="sxs-lookup"><span data-stu-id="9da17-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="9da17-407">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="9da17-407">Build the project.</span></span> <span data-ttu-id="9da17-408">Komut penceresine şunu girin:</span><span class="sxs-lookup"><span data-stu-id="9da17-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="9da17-409">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="9da17-409">The preceding commands:</span></span>

* <span data-ttu-id="9da17-410">*Geçişler/{zaman damgası} _RowVersion. cs* geçiş dosyasını ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="9da17-411">*Geçişler/SchoolContextModelSnapshot. cs* dosyasını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="9da17-412">Bu güncelleştirme yöntemine aşağıdaki vurgulanmış kodu ekler `BuildModel` :</span><span class="sxs-lookup"><span data-stu-id="9da17-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="9da17-413">VERITABANıNı güncelleştirmek için geçişleri çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="9da17-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="9da17-414">Departmanlar modelini temklesi</span><span class="sxs-lookup"><span data-stu-id="9da17-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9da17-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9da17-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="9da17-416">[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-student-pages) ve `Department` model sınıfı için kullanma bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="9da17-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9da17-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9da17-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="9da17-418">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9da17-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="9da17-419">Yukarıdaki komut, modeli bir daha yasaklıyor `Department` .</span><span class="sxs-lookup"><span data-stu-id="9da17-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="9da17-420">Projeyi Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="9da17-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="9da17-421">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="9da17-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="9da17-422">Departmanlar Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-422">Update the Departments Index page</span></span>

<span data-ttu-id="9da17-423">Scafkatlama altyapısı `RowVersion` Dizin sayfası için bir sütun oluşturdu, ancak bu alan gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="9da17-424">Bu öğreticide, `RowVersion` eşzamanlılık kavramasına yardımcı olmak için öğesinin son baytı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="9da17-425">Son baytın benzersiz olması garanti edilmez.</span><span class="sxs-lookup"><span data-stu-id="9da17-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="9da17-426">Gerçek bir uygulama, `RowVersion` veya son baytını göstermez `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="9da17-427">Dizin sayfasını güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="9da17-427">Update the Index page:</span></span>

* <span data-ttu-id="9da17-428">Dizini departmanlar ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9da17-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="9da17-429">İçeren biçimlendirmeyi `RowVersion` son baytla değiştirin `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="9da17-430">FirstMidName öğesini FullName ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9da17-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="9da17-431">Aşağıdaki biçimlendirme güncelleştirilmiş sayfayı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="9da17-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="9da17-432">Düzenleme sayfası modelini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="9da17-432">Update the Edit page model</span></span>

<span data-ttu-id="9da17-433">Aşağıdaki kodla *Pages\Departments\Edit.cshtml.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="9da17-434">Bir eşzamanlılık sorunu tespit etmek için [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) , `rowVersion` getirilen varlıktaki değeri ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="9da17-435">EF Core, özgün değeri içeren WHERE yan tümcesiyle bir SQL UPDATE komutu oluşturur `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="9da17-436">UPDATE komutundan hiçbir satır etkilenmeden (hiçbir satır özgün değere sahip değilse `RowVersion` ), bir `DbUpdateConcurrencyException` özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="9da17-437">Önceki kodda, `Department.RowVersion` varlık getirilirken değeridir.</span><span class="sxs-lookup"><span data-stu-id="9da17-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="9da17-438">`OriginalValue` Bu yöntemde çağrıldığında, DB 'deki değerdir `FirstOrDefaultAsync` .</span><span class="sxs-lookup"><span data-stu-id="9da17-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="9da17-439">Aşağıdaki kod, istemci değerlerini (Bu yönteme gönderilen değerler) ve DB değerlerini alır:</span><span class="sxs-lookup"><span data-stu-id="9da17-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="9da17-440">Aşağıdaki kod, ' den postalandıklarından farklı olarak DB değerleri olan her bir sütun için özel bir hata iletisi ekler `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="9da17-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="9da17-441">Aşağıdaki vurgulanan kod, `RowVersion` değeri, veritabanından alınan yeni değer olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9da17-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="9da17-442">Kullanıcının bir dahaki sefer **Kaydet** ' i tıklatması durumunda, yalnızca düzenleme sayfasının son görüntüsü bu yana gerçekleşen eşzamanlılık hataları yakalanacaktır.</span><span class="sxs-lookup"><span data-stu-id="9da17-442">The next time the user clicks **Save** , only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="9da17-443">`ModelState.Remove`Eski değere sahip olduğundan deyimin olması gerekir `ModelState` `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="9da17-444">RazorSayfada, `ModelState` bir alanın değeri, her ikisi de varsa Model Özellik değerlerinden önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="9da17-445">Düzenleme sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-445">Update the Edit page</span></span>

<span data-ttu-id="9da17-446">*Sayfaları/departmanları/Düzenle. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="9da17-447">Yukarıdaki biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="9da17-447">The preceding markup:</span></span>

* <span data-ttu-id="9da17-448">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="9da17-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9da17-449">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-449">Adds a hidden row version.</span></span> <span data-ttu-id="9da17-450">`RowVersion` , geri gönder değeri bağlayan için eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="9da17-451">`RowVersion`Hata ayıklama amacıyla son baytını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="9da17-452">`ViewData`Türü kesin belirlenmiş olan ile değiştirilir `InstructorNameSL` .</span><span class="sxs-lookup"><span data-stu-id="9da17-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="9da17-453">Düzenleme sayfasıyla eşzamanlılık çakışmalarını test etme</span><span class="sxs-lookup"><span data-stu-id="9da17-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="9da17-454">Ingilizce bölümünde düzenleme öğesinin iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="9da17-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="9da17-455">Uygulamayı çalıştırın ve Departmanlar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="9da17-456">Ingilizce departman için **düzenleme** köprüsüne sağ tıklayın ve **Yeni sekmede aç** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab** .</span></span>
* <span data-ttu-id="9da17-457">İlk sekmede, Ingilizce bölümünün **düzenleme** Köprüsü ' ne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="9da17-458">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9da17-459">İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-459">Change the name in the first browser tab and click **Save** .</span></span>

![Bölüm Düzenle sayfa 1 değişiklikten sonra](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="9da17-461">Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9da17-462">Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="9da17-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9da17-463">İkinci tarayıcı sekmesinden farklı bir alanı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9da17-463">Change a different field in the second browser tab.</span></span>

![Değişiklik sonrasında bölüm düzenleme sayfası 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="9da17-465">**Kaydet** ’e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-465">Click **Save** .</span></span> <span data-ttu-id="9da17-466">DB değerleriyle eşleşmeyen tüm alanlar için hata iletileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="9da17-466">You see error messages for all fields that don't match the DB values:</span></span>

![Bölüm düzenleme sayfası hata iletisi](concurrency/_static/edit-error.png)

<span data-ttu-id="9da17-468">Bu tarayıcı penceresi, ad alanını değiştirmeyi planlamadı.</span><span class="sxs-lookup"><span data-stu-id="9da17-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="9da17-469">Geçerli değeri (diller) kopyalayın ve ad alanına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="9da17-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="9da17-470">Sekme genişletme. İstemci tarafı doğrulaması hata iletisini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9da17-470">Tab out. Client-side validation removes the error message.</span></span>

![Bölüm düzenleme sayfası hata iletisi](concurrency/_static/cv.png)

<span data-ttu-id="9da17-472">Yeniden **Kaydet** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-472">Click **Save** again.</span></span> <span data-ttu-id="9da17-473">İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9da17-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="9da17-474">Kaydedilen değerleri Dizin sayfasında görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="9da17-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="9da17-475">Silme sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-475">Update the Delete page</span></span>

<span data-ttu-id="9da17-476">Silme sayfası modelini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="9da17-477">Sil sayfası, varlık alındıktan sonra değiştirildiğinde eşzamanlılık çakışmalarını algılar.</span><span class="sxs-lookup"><span data-stu-id="9da17-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="9da17-478">`Department.RowVersion` , varlık getirilen satır sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="9da17-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="9da17-479">EF Core, SQL DELETE komutunu oluşturduğunda, içeren bir WHERE yan tümcesi içerir `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="9da17-480">SQL DELETE komutu sıfır satırla etkileniyorsa:</span><span class="sxs-lookup"><span data-stu-id="9da17-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="9da17-481">`RowVersion`SQL DELETE komutunda bulunan, DB ile eşleşmiyor `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="9da17-482">Bir DbUpdateConcurrencyException özel durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9da17-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="9da17-483">`OnGetAsync` ile çağırılır `concurrencyError` .</span><span class="sxs-lookup"><span data-stu-id="9da17-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="9da17-484">Silme sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="9da17-484">Update the Delete page</span></span>

<span data-ttu-id="9da17-485">*Sayfaları/departmanları/delete. cshtml* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9da17-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="9da17-486">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="9da17-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="9da17-487">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="9da17-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9da17-488">Bir hata iletisi ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-488">Adds an error message.</span></span>
* <span data-ttu-id="9da17-489">FirstMidName öğesini, **yönetici** alanındaki FullName ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9da17-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="9da17-490">`RowVersion`Son baytın görüntüleneceği değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="9da17-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="9da17-491">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="9da17-491">Adds a hidden row version.</span></span> <span data-ttu-id="9da17-492">`RowVersion` , geri gönder değeri bağlayan için eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="9da17-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="9da17-493">Silme sayfasıyla eşzamanlılık çakışmalarını test etme</span><span class="sxs-lookup"><span data-stu-id="9da17-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="9da17-494">Test departmanı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9da17-494">Create a test department.</span></span>

<span data-ttu-id="9da17-495">Test departmanı üzerinde silmenin iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="9da17-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="9da17-496">Uygulamayı çalıştırın ve Departmanlar ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="9da17-497">Test departmanı için **silme** köprüsünü sağ tıklayın ve **Yeni sekmede aç** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9da17-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab** .</span></span>
* <span data-ttu-id="9da17-498">Test departmanı için **düzenleme** köprüsüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="9da17-499">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9da17-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9da17-500">İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet** ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="9da17-500">Change the budget in the first browser tab and click **Save** .</span></span>

<span data-ttu-id="9da17-501">Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9da17-502">Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="9da17-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9da17-503">İkinci sekmeden test departmanını silin. Bir eşzamanlılık hatası, VERITABANıNDAKI geçerli değerlerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="9da17-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="9da17-504">**Sil** ' i tıklatmak, güncelleştirilmemiş olmadığı takdirde varlığı siler `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="9da17-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="9da17-505">Veri modelinin nasıl devralınacağını öğrenmek için bkz. [Devralma](xref:data/ef-mvc/inheritance) .</span><span class="sxs-lookup"><span data-stu-id="9da17-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="9da17-506">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9da17-506">Additional resources</span></span>

* [<span data-ttu-id="9da17-507">EF Core eşzamanlılık belirteçleri</span><span class="sxs-lookup"><span data-stu-id="9da17-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="9da17-508">EF Core eşzamanlılık işle</span><span class="sxs-lookup"><span data-stu-id="9da17-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="9da17-509">Bu öğreticinin YouTube sürümü (eşzamanlılık çakışmalarını Işleme)</span><span class="sxs-lookup"><span data-stu-id="9da17-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="9da17-510">Bu öğreticinin YouTube sürümü (Bölüm 2)</span><span class="sxs-lookup"><span data-stu-id="9da17-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="9da17-511">Bu öğreticinin YouTube sürümü (Bölüm 3)</span><span class="sxs-lookup"><span data-stu-id="9da17-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="9da17-512">Önceki</span><span class="sxs-lookup"><span data-stu-id="9da17-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

