---
title: 'Öğretici: Eşpara kolu - EF Core ile ASP.NET MVC'
description: Bu öğretici, birden çok kullanıcı aynı varlığı aynı anda güncelleştirdiğinde çakışmaları nasıl işleyeceğini gösterir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 6839e383093b993ff55095f26cf88cd68708f001
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657397"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="5ac5b-103">Öğretici: Eşpara kolu - EF Core ile ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="5ac5b-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="5ac5b-104">Önceki öğreticilerde, verileri nasıl güncelleştirini öğrendiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="5ac5b-105">Bu öğretici, birden çok kullanıcı aynı varlığı aynı anda güncelleştirdiğinde çakışmaları nasıl işleyeceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="5ac5b-106">Bölüm kuruluşuyla çalışan ve eşzamanlılık hatalarını işleyen web sayfaları oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="5ac5b-107">Aşağıdaki çizimler, eşzamanlılık çakışması oluşursa görüntülenen bazı iletileri de içeren Düzenle ve Sil sayfalarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Bölüm Edit sayfası](concurrency/_static/edit-error.png)

![Bölüm Silme sayfası](concurrency/_static/delete-error.png)

<span data-ttu-id="5ac5b-110">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5ac5b-111">Eşzamanlılık çakışmaları hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="5ac5b-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="5ac5b-112">İzleme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="5ac5b-112">Add a tracking property</span></span>
> * <span data-ttu-id="5ac5b-113">Departmanlar denetleyicisi ve görünümleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="5ac5b-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="5ac5b-114">Dizin görünümünü güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-114">Update Index view</span></span>
> * <span data-ttu-id="5ac5b-115">Güncelle yöntemlerini güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-115">Update Edit methods</span></span>
> * <span data-ttu-id="5ac5b-116">Görünümü Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-116">Update Edit view</span></span>
> * <span data-ttu-id="5ac5b-117">Eşzamanlılık çakışmalarını test edin</span><span class="sxs-lookup"><span data-stu-id="5ac5b-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="5ac5b-118">Sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-118">Update the Delete page</span></span>
> * <span data-ttu-id="5ac5b-119">Ayrıntıları Güncelleştir ve Görünüm Oluştur</span><span class="sxs-lookup"><span data-stu-id="5ac5b-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5ac5b-120">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="5ac5b-120">Prerequisites</span></span>

* [<span data-ttu-id="5ac5b-121">İlgili verileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="5ac5b-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="5ac5b-122">Eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="5ac5b-122">Concurrency conflicts</span></span>

<span data-ttu-id="5ac5b-123">Eşzamanlılık çakışması, bir kullanıcı bir varlığın verilerini düzenlemek için görüntülediğinde ve ilk kullanıcı değişikliği veritabanına yazılmadan önce aynı varlığın verilerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="5ac5b-124">Bu tür çakışmaların algılanmasını etkinleştiremezseniz, veritabanını en son güncelalan kişi diğer kullanıcının değişikliklerinin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="5ac5b-125">Birçok uygulamada, bu risk kabul edilebilir: az sayıda kullanıcı veya birkaç güncelleştirme varsa veya bazı değişiklikler üzerine yazılmışsa gerçekten kritik değilse, eşzamanlılık için programlama maliyeti yarar dan daha ağır basabilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="5ac5b-126">Bu durumda, eşzamanlılık çakışmaları işlemek için uygulamayı yapılandırmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="5ac5b-127">Kötümser eşzamanlılık (kilitleme)</span><span class="sxs-lookup"><span data-stu-id="5ac5b-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="5ac5b-128">Uygulamanızın eşzamanlılık senaryolarında yanlışlıkla veri kaybını önlemesi gerekiyorsa, bunu yapmanın bir yolu veritabanı kilitlerini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="5ac5b-129">Buna kötümser eşzamanlılık denir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="5ac5b-130">Örneğin, veritabanındaki bir satırı okumadan önce, salt okunur veya güncelleştirme erişimi için bir kilit istersiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="5ac5b-131">Güncelleştirme erişimi için bir satırı kilitlerseniz, değiştirilme sürecindeolan verilerin bir kopyasını alacakları için başka kullanıcıların satırı salt okunur veya güncelleştirme erişimi için kilitlemesine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="5ac5b-132">Yalnızca okunur erişim için bir satırı kilitlerseniz, diğerleri yalnızca okunur erişim için de kilitleyebilir, ancak güncelleştirme için kilitlenmez.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="5ac5b-133">Kilitleri yönetmenin dezavantajları vardır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="5ac5b-134">Bu program için karmaşık olabilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-134">It can be complex to program.</span></span> <span data-ttu-id="5ac5b-135">Önemli veritabanı yönetim kaynakları gerektirir ve bir uygulamanın kullanıcı sayısı arttıkça performans sorunlarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="5ac5b-136">Bu nedenlerden dolayı, tüm veritabanı yönetim sistemleri kötümser eşzamanlılığı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="5ac5b-137">Entity Framework Core bunun için yerleşik destek sağlamaz ve bu öğretici bunu nasıl uygulayacağınızı göstermez.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="5ac5b-138">İyimser Eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="5ac5b-138">Optimistic Concurrency</span></span>

<span data-ttu-id="5ac5b-139">Kötümser eşzamanlılık alternatif iyimser eşzamanlılık olduğunu.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="5ac5b-140">İyimser eşzamanlılık, eşzamanlılık çakışmalarına izin vermek ve varsa uygun şekilde tepki vermek anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="5ac5b-141">Örneğin, Jane Bölüm Edit sayfasını ziyaret eder ve İngilizce bölümü için bütçe tutarını 350.000,00 TL'den 0,00 TL'ye değiştirir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget.png)

<span data-ttu-id="5ac5b-143">Jane **Kaydet'i**tıklatmadan önce, John aynı sayfayı ziyaret eder ve Başlangıç Tarihi alanını 9/1/2007'den 9/1/2013'e değiştirir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date.png)

<span data-ttu-id="5ac5b-145">Jane önce **Kaydet'i** tıklatıyor ve tarayıcı Dizin sayfasına döndüğünde değişikliğini görür.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Bütçe sıfıra değiştirildi](concurrency/_static/budget-zero.png)

<span data-ttu-id="5ac5b-147">Ardından John, 350.000,00 TL bütçegösteren bir Edit sayfasında **Kaydet'i** tıklatır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="5ac5b-148">Bundan sonra ne olacağı eşzamanlılık çakışmaları nasıl ele aldığınıza göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="5ac5b-149">Seçeneklerden bazıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-149">Some of the options include the following:</span></span>

* <span data-ttu-id="5ac5b-150">Bir kullanıcının hangi özelliği değiştirdiğini izleyebilir ve yalnızca veritabanındaki ilgili sütunları güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="5ac5b-151">Örnek senaryoda, farklı özellikler iki kullanıcı tarafından güncelleştirilmeden hiçbir veri kaybolmaz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="5ac5b-152">Bir dahaki sefere birisi İngilizce bölümüne göz atsa, jane ve John'un değişikliklerini görecekler -- 9/1/2013 başlangıç tarihi ve sıfır dolar lık bir bütçe.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="5ac5b-153">Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışma sayısını azaltabilir, ancak bir varlığın aynı özelliğinde rakip değişiklikler yapılırsa veri kaybını önleyemez.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="5ac5b-154">Varlık Çerçevesi'nin bu şekilde çalışıp çalışmadığına, güncelleştirme kodunuzu nasıl uyguladığınıza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="5ac5b-155">Bir kuruluşun tüm özgün özellik değerlerinin yanı sıra yeni değerleri izlemek için büyük miktarda durum korumanızı gerektirebileceğinden, web uygulamasında genellikle pratik değildir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="5ac5b-156">Sunucu kaynakları gerektirdiğinden veya web sayfasının kendisine (örneğin, gizli alanlarda) veya bir çereze eklenmesi gerektiğinden, büyük miktarda durum korumak uygulama performansını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="5ac5b-157">John'un değişmesine izin verebilirsin Jane'in bozuk paralarını yazabilirsin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="5ac5b-158">Birisi İngilizce bölümüne bir daha ki sefere göz atanın, 9/1/2013 ve geri yüklenen $350,000.00 değerini göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="5ac5b-159">Buna Müşteri *Kazançları* veya *Son Kazanç* senaryosu denir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="5ac5b-160">(İstemcinin tüm değerleri veri deposunda bulunanlardan önceliklidir.) Bu bölümün girişinde belirtildiği gibi, eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, bu otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="5ac5b-161">John'un değişikliğinin veritabanında güncelleştirilen olmasını engelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="5ac5b-162">Genellikle, bir hata iletisi görüntüler, ona verilerin geçerli durumunu gösterir ve hala bunları yapmak istiyorsa değişikliklerini yeniden uygulamasına izin verirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="5ac5b-163">Buna Mağaza *Kazançları* senaryosu denir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="5ac5b-164">(Veri depolama değerleri istemci tarafından gönderilen değerlerden önceliklidir.) Bu eğitimde Mağaza Kazançları senaryosunu uygularsınız.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="5ac5b-165">Bu yöntem, bir kullanıcı ne olup bittiği konusunda uyarılmadan hiçbir değişikliğin üzerine yazılmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="5ac5b-166">Eşzamanlılık çakışmalarını algılama</span><span class="sxs-lookup"><span data-stu-id="5ac5b-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="5ac5b-167">Varlık Çerçevesi'nin `DbConcurrencyException` attığı özel durumları işleyerek çakışmaları çözebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="5ac5b-168">Bu özel durumların ne zaman atılacağını bilmek için Varlık Çerçevesi'nin çakışmaları algılayabilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="5ac5b-169">Bu nedenle, veritabanını ve veri modelini uygun şekilde yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="5ac5b-170">Çakışma algılamayı etkinleştirmek için bazı seçenekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="5ac5b-171">Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemek için kullanılabilecek bir izleme sütunu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="5ac5b-172">Daha sonra Varlık Çerçevesi'ni SQL Update veya Delete komutlarının Where yan tümcesine bu sütunu ekecek şekilde yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="5ac5b-173">İzleme sütununun veri türü genellikle. `rowversion`</span><span class="sxs-lookup"><span data-stu-id="5ac5b-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="5ac5b-174">Değer, `rowversion` satır her güncelleştiride artılan sıralı bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="5ac5b-175">Bir Güncelleştirme veya Sil komutunda, Where yan tümcesi izleme sütununun (özgün satır sürümü) özgün değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="5ac5b-176">Güncelleştirilen satır başka bir kullanıcı tarafından değiştirildiyse, `rowversion` sütundaki değer özgün değerden farklıdır, bu nedenle Güncelleştirme veya Sil deyimi Where yan tümcesi nedeniyle güncellenecek satırı bulamıyor.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="5ac5b-177">Varlık Çerçevesi, Güncelleştirme veya Sil komutu tarafından hiçbir satırın güncelleştirilmediğini fark ettiğinde (diğer bir süre, etkilenen satır sayısı sıfır olduğunda), bunu eşzamanlılık çakışması olarak yorumlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="5ac5b-178">Varlık Çerçevesi'ni, Güncelleştirme ve Sil komutlarının Bulunduğu Yer yan tümcesindeki tablodaki her sütunun özgün değerlerini içerecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="5ac5b-179">İlk seçenekte olduğu gibi, satır ilk okundundan beri satırdaki herhangi bir şey değiştiyse, Nerede yan tümcesi güncelleştirmek için bir satır döndürmez ve Varlık Çerçevesi eşzamanlılık çakışması olarak yorumlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="5ac5b-180">Çok sayıda sütunu olan veritabanı tabloları için, bu yaklaşım çok büyük Where yan tümcelerine neden olabilir ve büyük miktarda durum korumanızı gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="5ac5b-181">Daha önce de belirtildiği gibi, büyük miktarda durum bakımı uygulama performansını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="5ac5b-182">Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="5ac5b-183">Eşzamanlılık için bu yaklaşımı uygulamak istiyorsanız, eşzamanlılığı izlemek istediğiniz varlıktaki tüm birincil anahtar olmayan özellikleri bunlara `ConcurrencyCheck` öznitelik ekleyerek işaretlemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="5ac5b-184">Bu değişiklik, Varlık Çerçevesi'nin Güncelleştirme ve Silme deyimlerinin SQL Where yan tümcesine tüm sütunları eklemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="5ac5b-185">Bu öğreticinin geri kalanında Bölüm `rowversion` kuruluşuna bir izleme özelliği ekler, bir denetleyici ve görünümler oluşturur ve her şeyin doğru çalıştığını doğrulamak için sınama sağlarsınız.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="5ac5b-186">İzleme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="5ac5b-186">Add a tracking property</span></span>

<span data-ttu-id="5ac5b-187">*Modeller/Department.cs*olarak, RowVersion adlı bir izleme özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="5ac5b-188">Öznitelik, `Timestamp` bu sütunun veritabanına gönderilen Güncelleştirme ve Sil komutlarının Nerede yan tümcesine dahil olacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="5ac5b-189">SQL Server'ın `Timestamp` önceki sürümleri, SQL `rowversion` yerine `timestamp` gelmeden önce bir SQL veri türü kullandığından öznitelik çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="5ac5b-190">.NET türü `rowversion` bir bayt dizisidir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="5ac5b-191">Akıcı API'yi kullanmayı tercih ederseniz, aşağıdaki `IsConcurrencyToken` örnekte gösterildiği gibi izleme özelliğini belirtmek için *(Data/SchoolContext.cs'de)* yöntemini kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="5ac5b-192">Bir özellik ekleyerek veritabanı modelini değiştirdiniz, bu nedenle başka bir geçiş yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="5ac5b-193">Değişikliklerinizi kaydedin ve projeyi oluşturun ve ardından komut penceresinde aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="5ac5b-194">Departmanlar denetleyicisi ve görünümleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="5ac5b-194">Create Departments controller and views</span></span>

<span data-ttu-id="5ac5b-195">Daha önce Öğrenciler, Kurslar ve Eğitmenler için yaptığınız gibi bir Bölüm denetleyicisi ve görüşlerini iskele.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![İskele Bölümü](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="5ac5b-197">*DepartmentsController.cs* dosyasında, bölüm yöneticisi açılır listelerin yalnızca soyadı yerine eğitmenin tam adını içermesini sağlayacak şekilde "FirstMidName" ile "FullName" dört olaylarını da değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="5ac5b-198">Dizin görünümünü güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-198">Update Index view</span></span>

<span data-ttu-id="5ac5b-199">İskele altyapısı Dizin görünümünde bir RowVersion sütunu oluşturdu, ancak bu alan görüntülenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="5ac5b-200">*Görünümler/Bölümler/Index.cshtml'deki* kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="5ac5b-201">Bu, başlığı "Bölümler" olarak değiştirir, RowVersion sütununa silinir ve yöneticinin ilk adı yerine tam adı gösterir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="5ac5b-202">Güncelle yöntemlerini güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-202">Update Edit methods</span></span>

<span data-ttu-id="5ac5b-203">Hem HttpGet `Edit` yönteminde `Details` hem de `AsNoTracking`yönteme .</span><span class="sxs-lookup"><span data-stu-id="5ac5b-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="5ac5b-204">HttpGet `Edit` yönteminde, Yönetici için istekli yükleme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="5ac5b-205">HttpPost `Edit` yönteminin varolan kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="5ac5b-206">Kod güncellenecek bölümü okumaya çalışarak başlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="5ac5b-207">Yöntem `FirstOrDefaultAsync` null dönerse, bölüm başka bir kullanıcı tarafından silinir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="5ac5b-208">Bu durumda kod, bir departman varlığı oluşturmak için deftere nakledilen form değerlerini kullanır, böylece Edit sayfası bir hata iletisi ile yeniden görüntülenebilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="5ac5b-209">Alternatif olarak, departman alanlarını yeniden görüntülemeden yalnızca bir hata iletisi görüntülerseniz, departman varlığını yeniden oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="5ac5b-210">Görünüm özgün `RowVersion` değeri gizli bir alanda depolar ve bu `rowVersion` yöntem bu değeri parametrede alır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="5ac5b-211">Aramadan `SaveChanges`önce, varlığın koleksiyonuna `RowVersion` `OriginalValues` özgün özellik değerini koymanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="5ac5b-212">Daha sonra Entity Framework bir SQL UPDATE komutu oluşturduğunda, bu komut özgün `RowVersion` değeri olan bir satırı arayan bir WHERE yan tümcesi içerir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="5ac5b-213">Güncelleştirme komutundan hiçbir satır etkilenmezse (hiçbir `RowVersion` satır özgün değere `DbUpdateConcurrencyException` sahip değilse), Varlık Çerçevesi bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="5ac5b-214">Bu özel durum için catch bloğundaki kod, özel durum nesnesindeki `Entries` özellikten güncelleştirilmiş değerlere sahip etkilenen Departman varlığını alır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="5ac5b-215">Koleksiyonda `Entries` yalnızca bir `EntityEntry` nesne olacaktır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="5ac5b-216">Kullanıcı tarafından girilen yeni değerleri ve geçerli veritabanı değerlerini almak için bu nesneyi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="5ac5b-217">Kod, kullanıcının Edit sayfasına girdiklerinden farklı veritabanı değerleri olan her sütun için özel bir hata iletisi ekler (kısaltma için burada yalnızca bir alan gösterilir).</span><span class="sxs-lookup"><span data-stu-id="5ac5b-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="5ac5b-218">Son olarak, kod `RowVersion` veritabanından `departmentToUpdate` alınan yeni değerin değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="5ac5b-219">Bu `RowVersion` yeni değer, Edit sayfası yeniden görüntülendiğinde gizli alanda depolanır ve kullanıcı **Kaydet'i**bir sonraki tıklattığında, yalnızca Edit sayfasının yeniden görüntülenmesinden bu yana oluşan eşzamanlılık hataları olur.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="5ac5b-220">Eski `ModelState.Remove` `RowVersion` değere `ModelState` sahip olduğundan ifade gereklidir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="5ac5b-221">Görünümde, bir `ModelState` alanın değeri, her ikisi de mevcut olduğunda model özellik değerlerinin üzerinde önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="5ac5b-222">Görünümü Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-222">Update Edit view</span></span>

<span data-ttu-id="5ac5b-223">*Görünümler/Bölümler/Edit.cshtml*olarak aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="5ac5b-224">`RowVersion` Özellik için gizli alanı hemen izleyen, özellik değerini kaydetmek için gizli bir alan `DepartmentID` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="5ac5b-225">Açılan listeye "Yönetici Seç" seçeneği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="5ac5b-226">Eşzamanlılık çakışmalarını test edin</span><span class="sxs-lookup"><span data-stu-id="5ac5b-226">Test concurrency conflicts</span></span>

<span data-ttu-id="5ac5b-227">Uygulamayı çalıştırın ve Bölümler Dizini sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="5ac5b-228">İngilizce bölümü için **Edit** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin, ardından İngilizce bölümü için **Edit** köprüünü tıklatın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="5ac5b-229">İki tarayıcı sekmesi artık aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="5ac5b-230">İlk tarayıcı sekmesinde bir alanı değiştirin ve **Kaydet'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-230">Change a field in the first browser tab and click **Save**.</span></span>

![Bölüm Değiştir'den sonra sayfa 1'i edit](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="5ac5b-232">Tarayıcı, değiştirilen değeri içeren Dizin sayfasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="5ac5b-233">İkinci tarayıcı sekmesinde bir alanı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-233">Change a field in the second browser tab.</span></span>

![Bölüm Değiştir'den sonra sayfa 2'yi edit](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="5ac5b-235">**Kaydet**'e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-235">Click **Save**.</span></span> <span data-ttu-id="5ac5b-236">Bir hata iletisi görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-236">You see an error message:</span></span>

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/edit-error.png)

<span data-ttu-id="5ac5b-238">Yeniden **Kaydet'i** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-238">Click **Save** again.</span></span> <span data-ttu-id="5ac5b-239">İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="5ac5b-240">Dizin sayfası görüntülendiğinde kaydedilen değerleri görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="5ac5b-241">Sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-241">Update the Delete page</span></span>

<span data-ttu-id="5ac5b-242">Sil sayfası için Varlık Çerçevesi, bölümü benzer bir şekilde düzenleyen başka birinin neden olduğu eşzamanlılık çakışmalarını algılar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="5ac5b-243">HttpGet `Delete` yöntemi onay görünümünü görüntülediğinde, görünüm `RowVersion` gizli bir alandaki özgün değeri içerir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="5ac5b-244">Bu değer daha sonra kullanıcı `Delete` silme onayladığı zaman çağrılan HttpPost yöntemi için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="5ac5b-245">Varlık Çerçevesi SQL DELETE komutunu oluşturduğunda, özgün `RowVersion` değeri olan bir WHERE yan tümcesi içerir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="5ac5b-246">Komut etkilenen sıfır satırla sonuçlanırsa (yani satır Sil onay sayfası görüntülendikten sonra değiştirildi), eşzamanlılık özel durumu atılır ve Onay sayfasını bir hata iletisiyle yeniden görüntülemek için httpget `Delete` yöntemi gerçek olarak ayarlanmış bir hata bayrağıyla çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="5ac5b-247">Satır başka bir kullanıcı tarafından silindiğinden sıfır satır etkilenmiş olabilir, bu nedenle bu durumda hata iletisi görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="5ac5b-248">Departmanlar denetleyicisinde Silme yöntemlerini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="5ac5b-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="5ac5b-249">*DepartmentsController.cs,* HttpGet `Delete` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="5ac5b-250">Yöntem, sayfanın eşzamanlılık hatasından sonra yeniden görüntülenip görüntülenmediğini gösteren isteğe bağlı bir parametre kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="5ac5b-251">Bu bayrak doğruysa ve belirtilen departman artık yoksa, başka bir kullanıcı tarafından silindi.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="5ac5b-252">Bu durumda, kod Dizin sayfasına yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="5ac5b-253">Bu bayrak doğruysa ve Bölüm varsa, başka bir kullanıcı tarafından değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="5ac5b-254">Bu durumda, kod kullanarak `ViewData`görünüme bir hata iletisi gönderir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="5ac5b-255">HttpPost `Delete` yöntemindeki kodu (adlandırılmış) `DeleteConfirmed`aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="5ac5b-256">Yeni değiştirdiğiniz iskele kodunda, bu yöntem yalnızca bir kayıt kimliği kabul etmiştir:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="5ac5b-257">Bu parametreyi model bağlayıcısı tarafından oluşturulan bir Departman varlık örneği olarak değiştirdiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="5ac5b-258">Bu, EF'nin kayıt anahtarına ek olarak RowVersion özellik değerine erişmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="5ac5b-259">Ayrıca eylem yöntemi adını da `DeleteConfirmed` `Delete`' dan ' a değiştirdiniz</span><span class="sxs-lookup"><span data-stu-id="5ac5b-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="5ac5b-260">İskele kodu, HttpPost yöntemine benzersiz bir imza vermek için bu adı `DeleteConfirmed` kullandı.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="5ac5b-261">(CLR farklı yöntem parametrelerine sahip olmak için aşırı yüklenen yöntemler gerektirir.) Artık imzalar benzersiz olduğuna göre, MVC kuralına bağlı kalabilir ve HttpPost ve HttpGet silme yöntemleri için aynı adı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="5ac5b-262">Bölüm zaten silinmişse, `AnyAsync` yöntem yanlış döndürür ve uygulama dizin yöntemine geri döner.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="5ac5b-263">Eşzamanlılık hatası yakalanırsa, kod Sil onay sayfasını yeniden görüntüler ve eşzamanlılık hatası iletisi görüntülemesi gerektiğini belirten bir bayrak sağlar.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="5ac5b-264">Sil görünümünü güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-264">Update the Delete view</span></span>

<span data-ttu-id="5ac5b-265">*Görünümler/Bölümler/Delete.cshtml'de,* İskele kodu, DepartmentID ve RowVersion özellikleri için bir hata iletisi alanı ve gizli alanlar ekleyen aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="5ac5b-266">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="5ac5b-267">Bu, aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-267">This makes the following changes:</span></span>

* <span data-ttu-id="5ac5b-268">Başlıklar arasında `h3` bir `h2` hata iletisi ekler.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="5ac5b-269">Yönetici **alanında** FirstMidName'i FullName ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="5ac5b-270">RowVersion alanını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="5ac5b-271">`RowVersion` Özellik için gizli bir alan ekler.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="5ac5b-272">Uygulamayı çalıştırın ve Bölümler Dizini sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="5ac5b-273">İngilizce bölümü için **Sil** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin, ardından ilk sekmede İngilizce bölümü için **Düzenle** köprübağlantısını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="5ac5b-274">İlk pencerede, değerlerden birini değiştirin ve **Kaydet'i**tıklatın:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-274">In the first window, change one of the values, and click **Save**:</span></span>

![Silmeden önce değişiklikten sonra Bölüm Düzenle sayfası](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="5ac5b-276">İkinci sekmede **Sil'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="5ac5b-277">Eşzamanlılık hata iletisini görürsünüz ve Departman değerleri veritabanında şu anda bulunanlarla yenilenir.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Bölüm Eşzamanlılık hatası ile onay sayfasını silme](concurrency/_static/delete-error.png)

<span data-ttu-id="5ac5b-279">Yeniden **Sil'i** tıklattığınızda, bölümün silindiğini gösteren Dizin sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="5ac5b-280">Ayrıntıları Güncelleştir ve Görünüm Oluştur</span><span class="sxs-lookup"><span data-stu-id="5ac5b-280">Update Details and Create views</span></span>

<span data-ttu-id="5ac5b-281">Ayrıntılar'daki iskele kodunu isteğe bağlı olarak temizleyebilir ve görünüm oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="5ac5b-282">RowVersion sütununa silmek ve Yöneticinin tam adını göstermek için *Görünümler/Bölümler/Details.cshtml'deki* kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="5ac5b-283">Açılan listeye Bir Seç seçeneği eklemek için *Görünümler/Bölümler/Create.cshtml'deki* kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="5ac5b-284">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="5ac5b-284">Get the code</span></span>

[<span data-ttu-id="5ac5b-285">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="5ac5b-286">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5ac5b-286">Additional resources</span></span>

 <span data-ttu-id="5ac5b-287">EF Core'da eşzamanlılık nasıl işleyeceğiniz hakkında daha fazla bilgi için [Eşzamanlılık çakışmaları'na](/ef/core/saving/concurrency)bakın.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5ac5b-288">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="5ac5b-288">Next steps</span></span>

<span data-ttu-id="5ac5b-289">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="5ac5b-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5ac5b-290">Eşzamanlılık çakışmaları hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="5ac5b-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="5ac5b-291">İzleme özelliği eklendi</span><span class="sxs-lookup"><span data-stu-id="5ac5b-291">Added a tracking property</span></span>
> * <span data-ttu-id="5ac5b-292">Oluşturulan Bölümler denetleyicisi ve görünümleri</span><span class="sxs-lookup"><span data-stu-id="5ac5b-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="5ac5b-293">Güncelleştirilmiş Dizin görünümü</span><span class="sxs-lookup"><span data-stu-id="5ac5b-293">Updated Index view</span></span>
> * <span data-ttu-id="5ac5b-294">Güncelleştirilmiş Edit yöntemleri</span><span class="sxs-lookup"><span data-stu-id="5ac5b-294">Updated Edit methods</span></span>
> * <span data-ttu-id="5ac5b-295">Güncelleştirilmiş Edit görünümü</span><span class="sxs-lookup"><span data-stu-id="5ac5b-295">Updated Edit view</span></span>
> * <span data-ttu-id="5ac5b-296">Test edilmiş eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="5ac5b-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="5ac5b-297">Sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="5ac5b-297">Updated the Delete page</span></span>
> * <span data-ttu-id="5ac5b-298">Güncelleştirilmiş Ayrıntılar ve Görünümler Oluştur</span><span class="sxs-lookup"><span data-stu-id="5ac5b-298">Updated Details and Create views</span></span>

<span data-ttu-id="5ac5b-299">Eğitmen ve Öğrenci kuruluşları için hiyerarşi başına tablo kalıbını nasıl uygulayacağınızı öğrenmek için bir sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="5ac5b-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5ac5b-300">Sonraki: Tablo başına hiyerarşi kalıtım uygulama</span><span class="sxs-lookup"><span data-stu-id="5ac5b-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
