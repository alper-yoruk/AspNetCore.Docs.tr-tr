---
title: 'Öğretici: bir ASP.NET MVC web uygulamasında EF Core kullanmaya başlama'
description: Bu sayfa, Contoso Üniversitesi örnek EF/MVC uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilk ilkisidir "
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365385"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="77a26-103">Öğretici: bir ASP.NET MVC web uygulamasında EF Core kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="77a26-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="77a26-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="77a26-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="77a26-105">Contoso Üniversitesi örnek Web AP, Entity Framework (EF) Core ve Visual Studio kullanarak ASP.NET Core MVC web uygulaması oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a26-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="77a26-106">Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="77a26-107">Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="77a26-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="77a26-108">Bu, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="77a26-109">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="77a26-109">Prerequisites</span></span>

* <span data-ttu-id="77a26-110">ASP.NET Core MVC 'yi yeni kullanmaya başladıysanız, bunu başlatmadan önce [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) öğreticisini kullanmaya başlayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="77a26-111">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="77a26-111">Database engines</span></span>

<span data-ttu-id="77a26-112">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="77a26-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="77a26-113">Sorunları çözün ve sorun giderin</span><span class="sxs-lookup"><span data-stu-id="77a26-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="77a26-114">Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="77a26-115">Yaygın hataların bir listesi ve bunların nasıl çözüleceği için, [serideki son öğreticinin sorun giderme bölümüne](advanced.md#common-errors)bakın.</span><span class="sxs-lookup"><span data-stu-id="77a26-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="77a26-116">İhtiyacınız olanları bulamazsanız, [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için bir soru gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="77a26-117">Bu, her biri daha önceki öğreticilerde gerçekleştirilen bir dizi 10 öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="77a26-118">Her başarılı öğreticinin tamamlanmasından sonra projenin bir kopyasını kaydetmeyi göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="77a26-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="77a26-119">Daha sonra sorunlarla karşılaşırsanız, tüm serinin başlangıcına dönmek yerine önceki öğreticiden baştan başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="77a26-120">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="77a26-120">Contoso University web app</span></span>

<span data-ttu-id="77a26-121">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="77a26-122">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="77a26-123">Uygulamadaki ekranlardan bazıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="77a26-123">Here are a few of the screens in the app:</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="77a26-126">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-126">Create web app</span></span>

* <span data-ttu-id="77a26-127">Visual Studio 'yu başlatın ve **ardından ASP.NET Core Web uygulaması** ' nı seçin > **NEXT**.</span><span class="sxs-lookup"><span data-stu-id="77a26-127">Start Visual Studio and select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="77a26-128">Projeyi adlandırın `ContosoUniversity` .</span><span class="sxs-lookup"><span data-stu-id="77a26-128">Name the project `ContosoUniversity`.</span></span> <span data-ttu-id="77a26-129">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="77a26-129">It's important to use this exact name including capitalization, so the namespaces match when code is copied.</span></span>
* <span data-ttu-id="77a26-130">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-130">Select **Create**.</span></span>
* <span data-ttu-id="77a26-131">Açılan menüden **.NET Core** ve **5,0 ASP.NET Core** seçin ve ardından **Web uygulaması (Model-View-Controller)** şablonu ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-131">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application (Model-View-Controller)** template.</span></span>
  <span data-ttu-id="77a26-132">![Yeni ASP.NET Core projesi iletişim kutusu](intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="77a26-132">![New ASP.NET Core Project dialog](intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="77a26-133">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="77a26-133">Set up the site style</span></span>

<span data-ttu-id="77a26-134">Birkaç temel değişiklik site menüsünü, düzeni ve giriş sayfasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77a26-134">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="77a26-135">*Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="77a26-135">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="77a26-136">Her oluşumunu ' a `ContosoUniversity` değiştirin `Contoso University` .</span><span class="sxs-lookup"><span data-stu-id="77a26-136">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="77a26-137">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="77a26-137">There are three occurrences.</span></span>
* <span data-ttu-id="77a26-138">**Hakkında** , **öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **Gizlilik** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="77a26-138">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="77a26-139">Yukarıdaki değişiklikler aşağıdaki kodda vurgulanır:</span><span class="sxs-lookup"><span data-stu-id="77a26-139">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="77a26-140">*Görünümler/Home/Index. cshtml* 'de, dosyanın içeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="77a26-140">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="77a26-141">Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-141">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="77a26-142">Giriş sayfası, bu öğreticide oluşturulan sayfaların sekmelerle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="77a26-142">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Contoso Üniversitesi ana sayfası](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="77a26-144">NuGet paketlerini EF Core</span><span class="sxs-lookup"><span data-stu-id="77a26-144">EF Core NuGet packages</span></span>

<span data-ttu-id="77a26-145">Bu öğretici SQL Server kullanır ve sağlayıcı paketi [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)' dır.</span><span class="sxs-lookup"><span data-stu-id="77a26-145">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="77a26-146">EF SQL Server paketi ve bağımlılıkları `Microsoft.EntityFrameworkCore` ve `Microsoft.EntityFrameworkCore.Relational` , EF için çalışma zamanı desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="77a26-146">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="77a26-147">[Microsoft. AspNetCore. Diagnostics. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ve [Microsoft. Aspnetcore. Diagnostics. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-147">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="77a26-148">Program Yöneticisi konsolunda (PMC), NuGet paketlerini eklemek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="77a26-148">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="77a26-149">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet paketi, EF Core hata sayfaları için ASP.NET Core ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="77a26-149">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="77a26-150">Bu ara yazılım EF Core geçişlerle hataları algılamaya ve tanılamanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="77a26-150">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="77a26-151">EF Core için kullanılabilen diğer veritabanı sağlayıcıları hakkında daha fazla bilgi için bkz. [veritabanı sağlayıcıları](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="77a26-151">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="77a26-152">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-152">Create the data model</span></span>

<span data-ttu-id="77a26-153">Bu uygulama için aşağıdaki varlık sınıfları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="77a26-153">The following entity classes are created for this app:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="77a26-155">Önceki varlıklarda aşağıdaki ilişkiler vardır:</span><span class="sxs-lookup"><span data-stu-id="77a26-155">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="77a26-156">Ve varlıkları arasında bire çok ilişki `Student` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="77a26-156">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="77a26-157">Bir öğrenci, herhangi bir sayıda kursa kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-157">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="77a26-158">Ve varlıkları arasında bire çok ilişki `Course` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="77a26-158">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="77a26-159">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-159">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="77a26-160">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="77a26-160">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="77a26-161">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="77a26-161">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="77a26-163">*Modeller* klasöründe, `Student` sınıfı aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77a26-163">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-164">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar ( **PK** ) sütunudur.</span><span class="sxs-lookup"><span data-stu-id="77a26-164">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="77a26-165">Varsayılan olarak, EF, `ID` birincil anahtar olarak veya adında bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-165">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="77a26-166">Örneğin, PK yerine adlandırılmış olabilir `StudentID` `ID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-166">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="77a26-167">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="77a26-167">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="77a26-168">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="77a26-168">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="77a26-169">`Enrollments`Bir `Student` varlığın özelliği:</span><span class="sxs-lookup"><span data-stu-id="77a26-169">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="77a26-170">`Enrollment`Bu varlıkla ilgili tüm varlıkları içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-170">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="77a26-171">Veritabanındaki belirli bir `Student` satırda iki ilişkili `Enrollment` satır varsa:</span><span class="sxs-lookup"><span data-stu-id="77a26-171">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="77a26-172">Bu `Student` varlığın `Enrollments` gezinti özelliği bu iki varlığı içerir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="77a26-172">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="77a26-173">`Enrollment` satırlar `StudentID` yabancı anahtar ( **FK** ) sütununda öğrencinin PK değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="77a26-173">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="77a26-174">Bir gezinti özelliği birden çok varlığı tutabilir:</span><span class="sxs-lookup"><span data-stu-id="77a26-174">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="77a26-175">Tür,, veya gibi bir liste olmalıdır `ICollection<T>` `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-175">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="77a26-176">Varlıklar eklenebilir, silinebilir ve güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-176">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="77a26-177">Çoka çok ve bire çok gezinti ilişkileri birden çok varlık içerebilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-177">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="77a26-178">Kullanıldığında `ICollection<T>` , EF `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a26-178">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="77a26-179">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="77a26-179">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="77a26-181">*Modeller* klasöründe, `Enrollment` sınıfı aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77a26-181">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-182">`EnrollmentID`Özelliği, ba.</span><span class="sxs-lookup"><span data-stu-id="77a26-182">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="77a26-183">Bu varlık `classnameID` kendi başına değil, kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-183">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="77a26-184">`Student`Varlık, bu `ID` kalıbı kullandı.</span><span class="sxs-lookup"><span data-stu-id="77a26-184">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="77a26-185">Bazı geliştiriciler, veri modeli genelinde bir model kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="77a26-185">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="77a26-186">Bu öğreticide, çeşitleme her iki düzenin de kullanılabileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a26-186">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="77a26-187">[Daha sonraki bir öğreticide](inheritance.md) `ID` , veri modelinde devralma uygulamayı nasıl daha kolay bir şekilde kullanacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a26-187">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="77a26-188">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="77a26-188">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="77a26-189">`?` `Grade` Tür bildiriminden sonra, `Grade` özelliğin [null değer atanabilir](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a26-189">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="77a26-190">Sıfır bir sınıfta farklı olan bir sınıf `null` .</span><span class="sxs-lookup"><span data-stu-id="77a26-190">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="77a26-191">`null` bir sınıf henüz bilinmediğini veya henüz atanmamış anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="77a26-191">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="77a26-192">`StudentID`Özelliği bir yabancı anahtardır (FK) ve buna karşılık gelen gezinti özelliği olur `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-192">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="77a26-193">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik yalnızca tek bir varlığı tutabilir `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-193">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="77a26-194">Bu, `Student.Enrollments` birden çok varlık içerebilen gezinti özelliğinden farklıdır `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="77a26-194">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="77a26-195">`CourseID`Özelliği BIR FK ve buna karşılık gelen gezinti özelliği `Course` .</span><span class="sxs-lookup"><span data-stu-id="77a26-195">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="77a26-196">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-196">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="77a26-197">Entity Framework, `<` gezinti özelliği adı `><` birincil anahtar özellik adı olarak adlandırılmışsa BIR özelliği FK özelliği olarak yorumlar `>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-197">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="77a26-198">Örneğin,, `StudentID` `Student` varlığın PK olduğundan, gezinti özelliği için `Student` `ID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-198">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="77a26-199">FK özellikleri,  `<` birincil anahtar özellik adı olarak da adlandırılabilir `>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-199">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="77a26-200">Örneğin, `CourseID` `Course` varlığın PK olduğu için `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-200">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="77a26-201">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="77a26-201">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="77a26-203">*Modeller* klasöründe, `Course` sınıfı aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77a26-203">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-204">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="77a26-205">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="77a26-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="77a26-206">[Databasegenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) özniteliği [sonraki bir öğreticide](complex-data-model.md)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="77a26-206">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="77a26-207">Bu öznitelik, veritabanının oluşturmak yerine kursa için PK girmeye izin verir.</span><span class="sxs-lookup"><span data-stu-id="77a26-207">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="77a26-208">Veritabanı bağlamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-208">Create the database context</span></span>

<span data-ttu-id="77a26-209">Belirli bir veri modeli için EF işlevini koordine eden ana sınıf <xref:Microsoft.EntityFrameworkCore.DbContext> veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="77a26-209">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="77a26-210">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="77a26-210">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="77a26-211">`DbContext`Türetilmiş sınıf, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="77a26-211">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="77a26-212">Bazı EF davranışları özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-212">Some EF behaviors can be customized.</span></span> <span data-ttu-id="77a26-213">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="77a26-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="77a26-214">Proje klasöründe adlı bir klasör oluşturun `Data` .</span><span class="sxs-lookup"><span data-stu-id="77a26-214">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="77a26-215">*Veri* klasöründe `SchoolContext` aşağıdaki kodla bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77a26-215">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-216">Yukarıdaki kod, her bir `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a26-216">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="77a26-217">EF terimlerinde:</span><span class="sxs-lookup"><span data-stu-id="77a26-217">In EF terminology:</span></span>

* <span data-ttu-id="77a26-218">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="77a26-218">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="77a26-219">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="77a26-219">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="77a26-220">`DbSet<Enrollment>`Ve `DbSet<Course>` deyimleri atlanabilir ve aynı şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="77a26-220">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="77a26-221">EF, bunları örtük olarak dahil eder çünkü:</span><span class="sxs-lookup"><span data-stu-id="77a26-221">EF would include them implicitly because:</span></span>

* <span data-ttu-id="77a26-222">`Student`Varlık `Enrollment` varlığa başvurur.</span><span class="sxs-lookup"><span data-stu-id="77a26-222">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="77a26-223">`Enrollment`Varlık `Course` varlığa başvurur.</span><span class="sxs-lookup"><span data-stu-id="77a26-223">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="77a26-224">Veritabanı oluşturulduğunda EF, özellik adlarıyla aynı adlara sahip tablolar oluşturur `DbSet` .</span><span class="sxs-lookup"><span data-stu-id="77a26-224">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="77a26-225">Koleksiyonlar için özellik adları genellikle plural ' dir.</span><span class="sxs-lookup"><span data-stu-id="77a26-225">Property names for collections are typically plural.</span></span> <span data-ttu-id="77a26-226">Örneğin, `Students` yerine `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-226">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="77a26-227">Geliştiriciler tablo adlarının plmış olup olmayacağını kabul etmez.</span><span class="sxs-lookup"><span data-stu-id="77a26-227">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="77a26-228">Bu öğreticiler için varsayılan davranış, içinde tekil tablo adları belirtilerek geçersiz kılınır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="77a26-228">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="77a26-229">Bunu yapmak için, son DbSet özelliğinden sonra aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="77a26-230">Kayıt defteri `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="77a26-230">Register the `SchoolContext`</span></span>

<span data-ttu-id="77a26-231">ASP.NET Core [bağımlılık ekleme](../../fundamentals/dependency-injection.md)işlemini içerir.</span><span class="sxs-lookup"><span data-stu-id="77a26-231">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="77a26-232">EF veritabanı bağlamı gibi hizmetler, uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-232">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="77a26-233">MVC denetleyicileri gibi bu hizmetleri gerektiren bileşenler, bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="77a26-233">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="77a26-234">Bir bağlam örneğini alan denetleyici Oluşturucu kodu, Bu öğreticinin ilerleyen kısımlarında gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="77a26-234">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="77a26-235">`SchoolContext`Hizmet olarak kaydetmek için *Startup.cs* açın ve vurgulanan satırları `ConfigureServices` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="77a26-236">Bağlantı dizesinin adı bir nesne üzerinde bir yöntem çağırarak bağlama geçirilir `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="77a26-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="77a26-237">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="77a26-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="77a26-238">Dosyayı açın *:::no-loc(appsettings.json):::* ve aşağıdaki biçimlendirmede gösterildiği gibi bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77a26-238">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="77a26-239">Veritabanı özel durum filtresini ekleme</span><span class="sxs-lookup"><span data-stu-id="77a26-239">Add the database exception filter</span></span>

<span data-ttu-id="77a26-240"><xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> `ConfigureServices` Aşağıdaki kodda gösterildiği gibi öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77a26-240">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="77a26-241">, `AddDatabaseDeveloperPageExceptionFilter` [Geliştirme ortamında](xref:fundamentals/environments)yararlı hata bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="77a26-241">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="77a26-242">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="77a26-242">SQL Server Express LocalDB</span></span>

<span data-ttu-id="77a26-243">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="77a26-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="77a26-244">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="77a26-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="77a26-245">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="77a26-245">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="77a26-246">Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-246">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="77a26-247">Test verileriyle VERITABANıNı başlatma</span><span class="sxs-lookup"><span data-stu-id="77a26-247">Initialize DB with test data</span></span>

<span data-ttu-id="77a26-248">EF boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a26-248">EF creates an empty database.</span></span> <span data-ttu-id="77a26-249">Bu bölümde, test verileriyle doldurmak için veritabanı oluşturulduktan sonra çağrılan bir yöntem eklenir.</span><span class="sxs-lookup"><span data-stu-id="77a26-249">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="77a26-250">`EnsureCreated`Yöntemi, veritabanını otomatik olarak oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-250">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="77a26-251">Sonraki bir [öğreticide](migrations.md), veritabanını bırakıp yeniden oluşturmak yerine veritabanı şemasını değiştirmek için Code First Migrations kullanarak model değişikliklerini nasıl işleyeceğinizi görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="77a26-251">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="77a26-252">*Veri* klasöründe, aşağıdaki kodla adlı yeni bir sınıf oluşturun `DbInitializer` :</span><span class="sxs-lookup"><span data-stu-id="77a26-252">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="77a26-253">Önceki kod, veritabanının mevcut olup olmadığını denetler:</span><span class="sxs-lookup"><span data-stu-id="77a26-253">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="77a26-254">Veritabanı bulunamazsa;</span><span class="sxs-lookup"><span data-stu-id="77a26-254">If the database is not found;</span></span>
  * <span data-ttu-id="77a26-255">Oluşturulur ve test verileriyle yüklenir.</span><span class="sxs-lookup"><span data-stu-id="77a26-255">It is created and loaded with test data.</span></span> <span data-ttu-id="77a26-256">Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-256">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="77a26-257">Bulunursa veritabanı hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="77a26-257">If the database if found, it takes no action.</span></span>

<span data-ttu-id="77a26-258">Aşağıdaki kodla *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77a26-258">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="77a26-259">*Program.cs* , uygulamanın başlangıcında şunları yapar:</span><span class="sxs-lookup"><span data-stu-id="77a26-259">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="77a26-260">Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.</span><span class="sxs-lookup"><span data-stu-id="77a26-260">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="77a26-261">Yöntemini çağırın `DbInitializer.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="77a26-261">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="77a26-262">`Initialize`Yöntemi aşağıdaki kodda gösterildiği gibi tamamlandığında bağlamı atın:</span><span class="sxs-lookup"><span data-stu-id="77a26-262">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="77a26-263">Uygulama ilk kez çalıştırıldığında, veritabanı oluşturulur ve test verileriyle yüklenir.</span><span class="sxs-lookup"><span data-stu-id="77a26-263">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="77a26-264">Veri modeli değiştiğinde:</span><span class="sxs-lookup"><span data-stu-id="77a26-264">Whenever the data model changes:</span></span>

* <span data-ttu-id="77a26-265">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="77a26-265">Delete the database.</span></span>
* <span data-ttu-id="77a26-266">Çekirdek yöntemini güncelleştirin ve baştan 'yi yeni bir veritabanı ile başlatın.</span><span class="sxs-lookup"><span data-stu-id="77a26-266">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="77a26-267">Sonraki öğreticilerde, veri modeli silindiğinde ve yeniden oluşturmadan veritabanı değişir.</span><span class="sxs-lookup"><span data-stu-id="77a26-267">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="77a26-268">Veri modeli değiştiğinde hiçbir veri kaybolmaz.</span><span class="sxs-lookup"><span data-stu-id="77a26-268">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="77a26-269">Denetleyici ve görünüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-269">Create controller and views</span></span>

<span data-ttu-id="77a26-270">Verileri sorgulamak ve kaydetmek için EF 'i kullanacak bir MVC denetleyicisi ve görünümleri eklemek için Visual Studio 'daki scafkatlama altyapısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="77a26-270">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="77a26-271">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, yapı iskelesi olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="77a26-271">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="77a26-272">**Çözüm Gezgini** , klasöre sağ tıklayın `Controllers` ve **> yeni yapı iskelesi öğesi Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-272">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="77a26-273">**Yapı Iskelesi Ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="77a26-273">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="77a26-274">**Entity Framework kullanarak, görünümlerle MVC denetleyicisi ' ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-274">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="77a26-275">**Ekle** 'ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-275">Click **Add**.</span></span> <span data-ttu-id="77a26-276">**Görünümler Ile MVC denetleyicisi ekleme, Entity Framework kullanma** iletişim kutusu görünür: ![ Yapı iskelesi öğrenci](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="77a26-276">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="77a26-277">**Model sınıfında** **öğrenci** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-277">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="77a26-278">**Veri bağlamı sınıfında** **SchoolContext** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-278">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="77a26-279">Varsayılan **Studentscontroller** adını olarak kabul edin.</span><span class="sxs-lookup"><span data-stu-id="77a26-279">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="77a26-280">**Ekle** 'ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-280">Click **Add**.</span></span>

<span data-ttu-id="77a26-281">Visual Studio yapı iskelesi altyapısı, `StudentsController.cs` denetleyicisiyle birlikte çalışan bir dosya ve bir dizi görünüm ( `*.cshtml` Dosyalar) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a26-281">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="77a26-282">Denetleyicinin bir `SchoolContext` Oluşturucu parametresi olarak aldığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-282">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="77a26-283">ASP.NET Core bağımlılığı ekleme, denetleyiciye bir örneğini geçirme işlemini gerçekleştirir `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="77a26-283">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="77a26-284">Bunu `Startup` sınıfında yapılandırdınız.</span><span class="sxs-lookup"><span data-stu-id="77a26-284">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="77a26-285">Denetleyici, `Index` veritabanındaki tüm öğrencileri görüntüleyen bir Action yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="77a26-285">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="77a26-286">Yöntemi, `Students` veritabanı bağlamı örneğinin özelliğini okuyarak öğrenciler varlık kümesinden öğrencilerin bir listesini alır:</span><span class="sxs-lookup"><span data-stu-id="77a26-286">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="77a26-287">Bu koddaki zaman uyumsuz programlama öğeleri öğreticide daha sonra açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="77a26-287">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="77a26-288">*Views/öğrenciler/Index. cshtml* görünümü bu listeyi bir tabloda görüntüler:</span><span class="sxs-lookup"><span data-stu-id="77a26-288">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="77a26-289">Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-289">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="77a26-290">Metodun eklendiği test verilerini görmek için öğrenciler sekmesine tıklayın `DbInitializer.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="77a26-290">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="77a26-291">Tarayıcı pencerenizin ne kadar dar olduğuna bağlı olarak `Students` sayfanın en üstünde sekme bağlantısını görürsünüz veya bağlantıyı görmek için sağ üst köşedeki gezinti simgesine tıklamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="77a26-291">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso Üniversitesi giriş sayfası dar](intro/_static/home-page-narrow.png)

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="77a26-294">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="77a26-294">View the database</span></span>

<span data-ttu-id="77a26-295">Uygulama başlatıldığında, `DbInitializer.Initialize` yöntemi çağırır `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="77a26-295">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="77a26-296">EF, veritabanı olmadığını gördük:</span><span class="sxs-lookup"><span data-stu-id="77a26-296">EF saw that there was no database:</span></span>

* <span data-ttu-id="77a26-297">Bu nedenle bir veritabanı oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="77a26-297">So it created a database.</span></span>
* <span data-ttu-id="77a26-298">`Initialize`Yöntem kodu, veritabanını verilerle doldurmuş.</span><span class="sxs-lookup"><span data-stu-id="77a26-298">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="77a26-299">Visual Studio 'da veritabanını görüntülemek için **SQL Server Nesne Gezgini** (ssox) kullanın:</span><span class="sxs-lookup"><span data-stu-id="77a26-299">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="77a26-300">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-300">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="77a26-301">SSOX 'te **(LocalDB) \MSSQLLocalDB > veritabanları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-301">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="77a26-302">`ContosoUniversity1`Dosyadaki bağlantı dizesinde bulunan veritabanı adı girişini seçin *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="77a26-302">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="77a26-303">Veritabanındaki tabloları görmek için **Tablolar** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="77a26-303">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX içindeki tablolar](intro/_static/ssox-tables.png)

<span data-ttu-id="77a26-305">**Öğrenci** tablosuna sağ tıklayın ve tablodaki verileri görmek Için **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-305">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![SSOX 'te öğrenci tablosu](intro/_static/ssox-student-table.png)

<span data-ttu-id="77a26-307">`*.mdf`Ve `*.ldf` veritabanı dosyaları *C:\Users \\ \<username>* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="77a26-307">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="77a26-308">, `EnsureCreated` Uygulama başlatma sırasında çalışan Başlatıcı yönteminde çağrıldığı için şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="77a26-308">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="77a26-309">Sınıfında bir değişiklik yapın `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-309">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="77a26-310">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="77a26-310">Delete the database.</span></span>
* <span data-ttu-id="77a26-311">Durdurun ve uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="77a26-311">Stop, then start the app.</span></span> <span data-ttu-id="77a26-312">Veritabanı, değişikliği eşleştirmek için otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="77a26-312">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="77a26-313">Örneğin, `EmailAddress` sınıfına bir özellik eklenirse `Student` , `EmailAddress` yeniden oluşturulan tablodaki yeni bir sütun.</span><span class="sxs-lookup"><span data-stu-id="77a26-313">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="77a26-314">Sınıflanmış görünüm yeni `EmailAddress` özelliği görüntülemez.</span><span class="sxs-lookup"><span data-stu-id="77a26-314">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="77a26-315">Kurallar</span><span class="sxs-lookup"><span data-stu-id="77a26-315">Conventions</span></span>

<span data-ttu-id="77a26-316">EF 'in bir bütün veritabanı oluşturması için yazılan kod miktarı, AŞV 'nin kullandığı kuralların kullanımı nedeniyle en az düzeydedir:</span><span class="sxs-lookup"><span data-stu-id="77a26-316">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="77a26-317">`DbSet`Özelliklerin adları tablo adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-317">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="77a26-318">Bir özellik tarafından başvurulmayan varlıklar için `DbSet` , varlık sınıfı adları tablo adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-318">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="77a26-319">Varlık özelliği adları, sütun adları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-319">Entity property names are used for column names.</span></span>
* <span data-ttu-id="77a26-320">Veya adlı varlık özellikleri `ID` `classnameID` PK özellikleri olarak tanınır.</span><span class="sxs-lookup"><span data-stu-id="77a26-320">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="77a26-321">Bir özellik, `<` gezinti özelliği adı `><` PK Özellik adı olarak ADLANDıRıLMıŞSA, FK özelliği olarak yorumlanır `>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-321">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="77a26-322">Örneğin,, `StudentID` `Student` varlığın PK olduğundan, gezinti özelliği için `Student` `ID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-322">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="77a26-323">FK özellikleri, `<` birincil anahtar özellik adı olarak da adlandırılabilir `>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-323">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="77a26-324">Örneğin, `EnrollmentID` `Enrollment` varlığın PK olduğundan `EnrollmentID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-324">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="77a26-325">Geleneksel davranış geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-325">Conventional behavior can be overridden.</span></span> <span data-ttu-id="77a26-326">Örneğin, bu öğreticide daha önce gösterildiği gibi tablo adları açıkça belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-326">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="77a26-327">Sütun adları ve herhangi bir özellik, bir PK veya FK olarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-327">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="77a26-328">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="77a26-328">Asynchronous code</span></span>

<span data-ttu-id="77a26-329">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="77a26-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="77a26-330">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="77a26-331">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="77a26-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="77a26-332">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="77a26-333">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="77a26-334">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="77a26-335">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir, ancak düşük trafik durumlarında performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-335">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="77a26-336">Aşağıdaki kodda,,, `async` `Task<T>` `await` ve `ToListAsync` kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="77a26-336">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="77a26-337">`async`Anahtar sözcüğü, derleyiciye Yöntem gövdesinin parçaları için geri çağrılar oluşturmasını ve döndürülen nesneyi otomatik olarak oluşturmasını söyler `Task<IActionResult>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-337">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="77a26-338">Dönüş türü, `Task<IActionResult>` türünde bir sonuçla devam eden işi temsil eder `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="77a26-338">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="77a26-339">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="77a26-339">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="77a26-340">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="77a26-340">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="77a26-341">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="77a26-341">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="77a26-342">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="77a26-342">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="77a26-343">EF kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="77a26-343">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="77a26-344">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="77a26-344">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="77a26-345">Bu, örneğin,, `ToListAsync` ve içerir `SingleOrDefaultAsync` `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="77a26-345">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="77a26-346">Örneğin, gibi yalnızca bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="77a26-346">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="77a26-347">EF bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-347">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="77a26-348">Herhangi bir zaman uyumsuz EF yöntemini çağırdığınızda, her zaman `await` anahtar sözcüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="77a26-348">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="77a26-349">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, kullanılan tüm kitaplık paketlerinin, sorguların veritabanına gönderilmesine neden olan bir EF yöntemi çağırıyorsa, zaman uyumsuz olarak da kullanıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="77a26-349">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="77a26-350">.NET ' te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [Async Overview](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="77a26-350">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="77a26-351">Getirilen varlıkları sınırla</span><span class="sxs-lookup"><span data-stu-id="77a26-351">Limit entities fetched</span></span>

<span data-ttu-id="77a26-352">Bir sorgudan döndürülen sayı veya varlıkları sınırlama hakkında bilgi için bkz. [performans konuları](xref:data/ef-rp/intro) .</span><span class="sxs-lookup"><span data-stu-id="77a26-352">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="77a26-353">Temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerini nasıl gerçekleştireceğinizi öğrenmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-353">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="77a26-354">Temel CRUD işlevlerini uygulama</span><span class="sxs-lookup"><span data-stu-id="77a26-354">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="77a26-355">Contoso Üniversitesi örnek Web uygulaması, Entity Framework (EF) Core 2,2 ve Visual Studio 2017 veya 2019 kullanarak ASP.NET Core 2,2 MVC web uygulamalarının nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a26-355">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="77a26-356">Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-356">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="77a26-357">Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="77a26-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="77a26-358">Bu, Contoso Üniversitesi örnek uygulamasının sıfırdan nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-358">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="77a26-359">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="77a26-359">Prerequisites</span></span>

* [<span data-ttu-id="77a26-360">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="77a26-360">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="77a26-361">Aşağıdaki iş yükleriyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) :</span><span class="sxs-lookup"><span data-stu-id="77a26-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="77a26-362">**ASP.net ve Web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="77a26-362">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="77a26-363">**.NET Core platformlar arası geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="77a26-363">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="77a26-364">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="77a26-364">Troubleshooting</span></span>

<span data-ttu-id="77a26-365">Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-365">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="77a26-366">Yaygın hataların bir listesi ve bunların nasıl çözüleceği için, [serideki son öğreticinin sorun giderme bölümüne](advanced.md#common-errors)bakın.</span><span class="sxs-lookup"><span data-stu-id="77a26-366">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="77a26-367">İhtiyacınız olanları bulamazsanız, [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için bir soru gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-367">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="77a26-368">Bu, her biri daha önceki öğreticilerde gerçekleştirilen bir dizi 10 öğreticisidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-368">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="77a26-369">Her başarılı öğreticinin tamamlanmasından sonra projenin bir kopyasını kaydetmeyi göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="77a26-369">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="77a26-370">Daha sonra sorunlarla karşılaşırsanız, tüm serinin başlangıcına dönmek yerine önceki öğreticiden baştan başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-370">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="77a26-371">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="77a26-371">Contoso University web app</span></span>

<span data-ttu-id="77a26-372">Bu öğreticilerde oluşturacağınız uygulama basit bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-372">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="77a26-373">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="77a26-374">Oluşturacağınız ekranların bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="77a26-374">Here are a few of the screens you'll create.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="77a26-377">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-377">Create web app</span></span>

* <span data-ttu-id="77a26-378">Visual Studio'yu açın.</span><span class="sxs-lookup"><span data-stu-id="77a26-378">Open Visual Studio.</span></span>

* <span data-ttu-id="77a26-379">**Dosya** menüsünden **Yeni > proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-379">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="77a26-380">Sol bölmeden, **yüklü > Visual C# > Web** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-380">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="77a26-381">**ASP.NET Core Web uygulaması** proje şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-381">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="77a26-382">Ad olarak **Contosouniversity** yazın ve **Tamam** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-382">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Yeni Proje iletişim kutusu](intro/_static/new-project2.png)

* <span data-ttu-id="77a26-384">**Yeni ASP.NET Core Web uygulaması** iletişim kutusunun görünmesini bekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-384">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="77a26-385">**.NET Core** , **ASP.NET Core 2,2** ve **Web uygulaması (Model-View-Controller)** şablonu ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-385">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="77a26-386">**Kimlik doğrulamanın** **kimlik doğrulaması yok** olarak ayarlandığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="77a26-386">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="77a26-387">**Tamam** 'ı seçin</span><span class="sxs-lookup"><span data-stu-id="77a26-387">Select **OK**</span></span>

  ![Yeni ASP.NET Core projesi iletişim kutusu](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="77a26-389">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="77a26-389">Set up the site style</span></span>

<span data-ttu-id="77a26-390">Birkaç basit değişiklik, site menüsünü, düzeni ve giriş sayfasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77a26-390">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="77a26-391">*Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="77a26-391">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="77a26-392">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77a26-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="77a26-393">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="77a26-393">There are three occurrences.</span></span>

* <span data-ttu-id="77a26-394">**Hakkında** , **öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **Gizlilik** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="77a26-394">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="77a26-395">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="77a26-395">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="77a26-396">*Görünümler/Home/Index. cshtml* 'de, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="77a26-396">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="77a26-397">Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-397">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="77a26-398">Bu öğreticilerde oluşturacağınız sayfaların sekmelerini içeren giriş sayfasını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="77a26-398">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso Üniversitesi ana sayfası](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="77a26-400">NuGet paketleri EF Core hakkında</span><span class="sxs-lookup"><span data-stu-id="77a26-400">About EF Core NuGet packages</span></span>

<span data-ttu-id="77a26-401">Bir projeye EF Core destek eklemek için hedeflemek istediğiniz veritabanı sağlayıcısını yükleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-401">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="77a26-402">Bu öğretici SQL Server kullanır ve sağlayıcı paketi [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)' dır.</span><span class="sxs-lookup"><span data-stu-id="77a26-402">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="77a26-403">Bu paket [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur, bu nedenle pakete başvurmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="77a26-403">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="77a26-404">EF SQL Server paketi ve bağımlılıkları ( `Microsoft.EntityFrameworkCore` ve `Microsoft.EntityFrameworkCore.Relational` ) EF için çalışma zamanı desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="77a26-404">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="77a26-405">[Geçiş](migrations.md) öğreticisinde daha sonra bir araç paketi ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-405">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="77a26-406">Entity Framework Core için kullanılabilen diğer veritabanı sağlayıcıları hakkında daha fazla bilgi için bkz. [veritabanı sağlayıcıları](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="77a26-406">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="77a26-407">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-407">Create the data model</span></span>

<span data-ttu-id="77a26-408">Daha sonra Contoso Üniversitesi uygulaması için varlık sınıfları oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="77a26-408">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="77a26-409">Aşağıdaki üç varlıkla başlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="77a26-409">You'll start with the following three entities.</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="77a26-411">Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` ve ile varlıklar arasında bire çok ilişki vardır `Course` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="77a26-411">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="77a26-412">Diğer bir deyişle, bir öğrenci herhangi bir sayıda kursa kaydedilebilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-412">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="77a26-413">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="77a26-413">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="77a26-414">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="77a26-414">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="77a26-416">*Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyası oluşturun ve şablon kodunu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77a26-416">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-417">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olacak.</span><span class="sxs-lookup"><span data-stu-id="77a26-417">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="77a26-418">Varsayılan olarak Entity Framework, `ID` veya `classnameID` olarak adlandırılan özellikleri birincil anahtar olarak algılar.</span><span class="sxs-lookup"><span data-stu-id="77a26-418">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="77a26-419">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="77a26-419">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="77a26-420">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="77a26-420">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="77a26-421">Bu durumda, `Enrollments` öğesinin özelliği bu `Student entity` `Enrollment` varlıkla ilgili varlıkların tümünü tutacaktır `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-421">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="77a26-422">Diğer bir deyişle, veritabanındaki bir `Student` satırda iki ilişkili satır varsa (söz konusu `Enrollment` öğrencinin birincil anahtar değerini kendi Studentitıd yabancı anahtar sütununda içeren satırlar) varsa, bu `Student` varlığın `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerecektir.</span><span class="sxs-lookup"><span data-stu-id="77a26-422">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="77a26-423">Bir gezinti özelliği birden çok varlığı tutabileceiyorsa (çok-çok veya bire çok ilişkilerde olduğu gibi), türü girişlerin eklenebileceği, silinebileceği ve güncelleştirilemeyebilir bir liste olmalıdır `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-423">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="77a26-424">Veya gibi `ICollection<T>` bir tür belirtebilirsiniz `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-424">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="77a26-425">Belirtirseniz `ICollection<T>` , EF `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a26-425">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="77a26-426">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="77a26-426">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="77a26-428">*Modeller* klasöründe *enrollment.cs* oluşturun ve mevcut kodu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="77a26-428">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-429">`EnrollmentID`Özelliği birincil anahtar olacaktır; bu varlık, `classnameID` `ID` varlıkta gördüğünüz gibi kendi başına bir model kullanır `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-429">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="77a26-430">Normalde tek bir model seçip veri modeliniz genelinde kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-430">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="77a26-431">Burada, değişim, her iki stili de kullanabileceğinizi gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a26-431">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="77a26-432">[Daha sonraki bir öğreticide](inheritance.md), ID 'yi ClassName olmadan kullanarak, veri modelinde devralma uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="77a26-432">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="77a26-433">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="77a26-433">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="77a26-434">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a26-434">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="77a26-435">Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="77a26-435">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="77a26-436">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="77a26-436">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="77a26-437">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik yalnızca tek bir varlığı tutabilir `Student` ( `Student.Enrollments` daha önce gördüğünüz gezinti özelliğinden farklı olarak, birden çok `Enrollment` varlık tutabilir).</span><span class="sxs-lookup"><span data-stu-id="77a26-437">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="77a26-438">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="77a26-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="77a26-439">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="77a26-440">Entity Framework, bir özelliği bir yabancı anahtar özelliği olarak `<navigation property name><primary key property name>` (örneğin, `StudentID` `Student` varlığın birincil anahtarından bu yana gezinti özelliği için) olarak yorumlar `Student` `ID` .</span><span class="sxs-lookup"><span data-stu-id="77a26-440">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="77a26-441">Yabancı anahtar özellikleri de yalnızca adlandırılmış olabilir `<primary key property name>` (örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` ).</span><span class="sxs-lookup"><span data-stu-id="77a26-441">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="77a26-442">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="77a26-442">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="77a26-444">*Modeller* klasöründe *Course.cs* oluşturun ve mevcut kodu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="77a26-444">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-445">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-445">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="77a26-446">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="77a26-446">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="77a26-447">`DatabaseGenerated`Bu serinin [sonraki bir öğreticide](complex-data-model.md) özniteliği hakkında daha fazla bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-447">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="77a26-448">Temel olarak bu öznitelik, veritabanının oluşturması yerine kursa ait birincil anahtarı girmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="77a26-448">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="77a26-449">Veritabanı bağlamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-449">Create the database context</span></span>

<span data-ttu-id="77a26-450">Belirli bir veri modeli için Entity Framework işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="77a26-450">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="77a26-451">Sınıfından türeterek bu sınıfı oluşturursunuz `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="77a26-451">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="77a26-452">Kodunuzda, veri modeline hangi varlıkların ekleneceğini belirtirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-452">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="77a26-453">Ayrıca, belirli Entity Framework davranışlarını özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-453">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="77a26-454">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="77a26-454">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="77a26-455">Proje klasöründe, *veri* adlı bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77a26-455">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="77a26-456">*Veri* klasöründe, *SchoolContext.cs* adlı yeni bir sınıf dosyası oluşturun ve şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="77a26-456">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-457">Bu kod, `DbSet` her bir varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a26-457">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="77a26-458">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="77a26-458">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="77a26-459">Ve deyimlerini atlamış olabilirsiniz `DbSet<Enrollment>` `DbSet<Course>` ve aynı şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="77a26-459">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="77a26-460">Entity Framework, `Student` varlık varlığa başvurduğundan ve varlık varlığa başvurduğundan bunları örtülü olarak içerebilir `Enrollment` `Enrollment` `Course` .</span><span class="sxs-lookup"><span data-stu-id="77a26-460">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="77a26-461">Veritabanı oluşturulduğunda EF, özellik adlarıyla aynı adlara sahip tablolar oluşturur `DbSet` .</span><span class="sxs-lookup"><span data-stu-id="77a26-461">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="77a26-462">Koleksiyonlar için özellik adları genellikle plural (öğrenci yerine öğrenciler), ancak geliştiriciler tablo adlarının plurulululmasını kabul etmez.</span><span class="sxs-lookup"><span data-stu-id="77a26-462">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="77a26-463">Bu öğreticiler için DbContext içinde tekil tablo adları belirterek varsayılan davranışı geçersiz kılarsınız.</span><span class="sxs-lookup"><span data-stu-id="77a26-463">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="77a26-464">Bunu yapmak için, son DbSet özelliğinden sonra aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-464">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="77a26-465">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-465">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="77a26-466">SchoolContext kaydetme</span><span class="sxs-lookup"><span data-stu-id="77a26-466">Register the SchoolContext</span></span>

<span data-ttu-id="77a26-467">ASP.NET Core, varsayılan olarak [bağımlılık ekleme](../../fundamentals/dependency-injection.md) işlemini uygular.</span><span class="sxs-lookup"><span data-stu-id="77a26-467">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="77a26-468">Hizmetler (EF veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-468">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="77a26-469">Bu hizmetleri gerektiren bileşenler (MVC denetleyicileri gibi) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="77a26-469">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="77a26-470">Bu öğreticide daha sonra bir bağlam örneği alan denetleyici Oluşturucu kodunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="77a26-470">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="77a26-471">`SchoolContext`Hizmet olarak kaydetmek için *Startup.cs* açın ve vurgulanan satırları `ConfigureServices` yöntemine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-471">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="77a26-472">Bağlantı dizesinin adı bir nesne üzerinde bir yöntem çağırarak bağlama geçirilir `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="77a26-472">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="77a26-473">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="77a26-473">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="77a26-474">`using` `ContosoUniversity.Data` Ve ad alanları için deyimler ekleyin `Microsoft.EntityFrameworkCore` ve ardından projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-474">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="77a26-475">Dosyayı açın *:::no-loc(appsettings.json):::* ve aşağıdaki örnekte gösterildiği gibi bir bağlantı dizesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-475">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="77a26-476">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="77a26-476">SQL Server Express LocalDB</span></span>

<span data-ttu-id="77a26-477">Bağlantı dizesi bir SQL Server LocalDB veritabanı belirtir.</span><span class="sxs-lookup"><span data-stu-id="77a26-477">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="77a26-478">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="77a26-478">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="77a26-479">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="77a26-479">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="77a26-480">Varsayılan olarak, LocalDB dizinde *. mdf* veritabanı dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-480">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="77a26-481">Test verileriyle VERITABANıNı başlatma</span><span class="sxs-lookup"><span data-stu-id="77a26-481">Initialize DB with test data</span></span>

<span data-ttu-id="77a26-482">Entity Framework, sizin için boş bir veritabanı oluşturacaktır.</span><span class="sxs-lookup"><span data-stu-id="77a26-482">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="77a26-483">Bu bölümde, test verileriyle doldurmak için veritabanı oluşturulduktan sonra çağrılan bir yöntem yazarsınız.</span><span class="sxs-lookup"><span data-stu-id="77a26-483">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="77a26-484">Burada, `EnsureCreated` veritabanını otomatik olarak oluşturmak için yöntemini kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="77a26-484">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="77a26-485">Sonraki bir [öğreticide](migrations.md) , veritabanını bırakıp yeniden oluşturmak yerine veritabanı şemasını değiştirmek için Code First Migrations kullanarak model değişikliklerini nasıl işleyeceğinizi göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-485">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="77a26-486">*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve şablon kodunu, gerektiğinde bir veritabanının oluşturulmasına neden olan aşağıdaki kodla değiştirin ve test verilerini yeni veritabanına yükler.</span><span class="sxs-lookup"><span data-stu-id="77a26-486">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="77a26-487">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler ve yoksa, veritabanının yeni olduğunu ve test verileriyle hazırlanması gerektiğini varsayar.</span><span class="sxs-lookup"><span data-stu-id="77a26-487">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="77a26-488">Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-488">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="77a26-489">*Program.cs* ' de, `Main` Uygulama başlangıcında aşağıdakini yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="77a26-489">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="77a26-490">Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.</span><span class="sxs-lookup"><span data-stu-id="77a26-490">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="77a26-491">Temel yöntemi çağırın ve bu yönteme geçerek bağlamı geçer.</span><span class="sxs-lookup"><span data-stu-id="77a26-491">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="77a26-492">Çekirdek yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="77a26-492">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="77a26-493">Uygulamayı ilk kez çalıştırdığınızda veritabanı oluşturulur ve test verileriyle birlikte gösterilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-493">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="77a26-494">Veri modelini her değiştirdiğinizde:</span><span class="sxs-lookup"><span data-stu-id="77a26-494">Whenever you change the data model:</span></span>

 * <span data-ttu-id="77a26-495">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="77a26-495">Delete the database.</span></span>
 * <span data-ttu-id="77a26-496">Çekirdek yöntemini güncelleştirin ve yeni bir veritabanıyla aynı şekilde bir baştan başlatın.</span><span class="sxs-lookup"><span data-stu-id="77a26-496">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="77a26-497">Sonraki öğreticilerde, veri modeli değiştiğinde ve yeniden oluşturmadan veritabanını nasıl değiştireceğiniz hakkında bilgi edineceksiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-497">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="77a26-498">Denetleyici ve görünüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="77a26-498">Create controller and views</span></span>

<span data-ttu-id="77a26-499">Bu bölümde, verileri sorgulamak ve kaydetmek için EF 'i kullanacak bir MVC denetleyicisi ve görünümleri eklemek için Visual Studio 'daki scafkatlama altyapısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-499">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="77a26-500">CRUD eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, yapı iskelesi olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="77a26-500">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="77a26-501">Yapı iskelesi, yapı oluşturma işleminden farklı olarak, kendi gereksinimlerinize uyacak şekilde değiştirebileceğiniz bir başlangıç noktası ve genellikle oluşturulan kodu değiştirmezsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-501">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="77a26-502">Oluşturulan kodu özelleştirmeniz gerektiğinde, kısmi sınıfları kullanırsınız veya işlemler değiştiğinde kodu yeniden oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="77a26-502">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="77a26-503">**Çözüm Gezgini** ' de **denetleyiciler** klasörüne sağ tıklayın ve **> yeni iskele öğe Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-503">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="77a26-504">**Yapı Iskelesi Ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="77a26-504">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="77a26-505">**Entity Framework kullanarak, görünümlerle MVC denetleyicisi ' ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-505">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="77a26-506">**Ekle** 'ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-506">Click **Add**.</span></span> <span data-ttu-id="77a26-507">**Görünümler Ile MVC denetleyicisi ekleme, Entity Framework kullanma** iletişim kutusu görünür: ![ Yapı iskelesi öğrenci](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="77a26-507">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="77a26-508">**Model sınıfı** ' nda **öğrenci** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-508">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="77a26-509">**Veri bağlamı sınıfında** **SchoolContext** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-509">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="77a26-510">Varsayılan **Studentscontroller** adını olarak kabul edin.</span><span class="sxs-lookup"><span data-stu-id="77a26-510">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="77a26-511">**Ekle** 'ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-511">Click **Add**.</span></span>

<span data-ttu-id="77a26-512">Visual Studio yapı iskelesi altyapısı, denetleyicisiyle birlikte çalışan bir *StudentsController.cs* dosyası ve bir dizi görünüm ( *. cshtml* dosyası) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a26-512">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="77a26-513">Denetleyicinin bir `SchoolContext` Oluşturucu parametresi olarak aldığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-513">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="77a26-514">ASP.NET Core bağımlılığı ekleme, denetleyiciye bir örneğini geçirme işlemini gerçekleştirir `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="77a26-514">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="77a26-515">Bu, *Startup.cs* dosyasında yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="77a26-515">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="77a26-516">Denetleyici, `Index` veritabanındaki tüm öğrencileri görüntüleyen bir Action yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="77a26-516">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="77a26-517">Yöntemi, `Students` veritabanı bağlamı örneğinin özelliğini okuyarak öğrenciler varlık kümesinden öğrencilerin bir listesini alır:</span><span class="sxs-lookup"><span data-stu-id="77a26-517">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="77a26-518">Bu koddaki zaman uyumsuz programlama öğelerini öğreticide daha sonra öğrenirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-518">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="77a26-519">*Views/öğrenciler/Index. cshtml* görünümü bu listeyi bir tabloda görüntüler:</span><span class="sxs-lookup"><span data-stu-id="77a26-519">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="77a26-520">Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-520">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="77a26-521">Metodun eklendiği test verilerini görmek için öğrenciler sekmesine tıklayın `DbInitializer.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="77a26-521">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="77a26-522">Tarayıcı pencerenizin ne kadar dar olduğuna bağlı olarak `Students` sayfanın en üstünde sekme bağlantısını görürsünüz veya bağlantıyı görmek için sağ üst köşedeki gezinti simgesine tıklamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="77a26-522">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso Üniversitesi giriş sayfası dar](intro/_static/home-page-narrow.png)

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="77a26-525">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="77a26-525">View the database</span></span>

<span data-ttu-id="77a26-526">Uygulamayı başlattığınızda, `DbInitializer.Initialize` yöntemi çağırır `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="77a26-526">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="77a26-527">EF, bir veritabanı olmadığını ve bu nedenle bir tane oluşturduğunu gördük, daha sonra `Initialize` Yöntem kodunun geri kalanı veritabanını verilerle doldurmuş.</span><span class="sxs-lookup"><span data-stu-id="77a26-527">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="77a26-528">Visual Studio 'da veritabanını görüntülemek için **SQL Server Nesne Gezgini** (ssox) kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-528">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="77a26-529">Tarayıcıyı kapatın.</span><span class="sxs-lookup"><span data-stu-id="77a26-529">Close the browser.</span></span>

<span data-ttu-id="77a26-530">SSOX penceresi henüz açık değilse, Visual Studio 'daki **Görünüm** menüsünden bunu seçin.</span><span class="sxs-lookup"><span data-stu-id="77a26-530">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="77a26-531">SSOX 'te **(LocalDB) \MSSQLLocalDB > veritabanları** ' na tıklayın ve ardından dosyadaki bağlantı dizesinde bulunan veritabanı adı için girişe tıklayın *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="77a26-531">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="77a26-532">Veritabanındaki tabloları görmek için **Tablolar** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="77a26-532">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX içindeki tablolar](intro/_static/ssox-tables.png)

<span data-ttu-id="77a26-534">Oluşturulan sütunları ve tabloya eklenmiş satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-534">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX 'te öğrenci tablosu](intro/_static/ssox-student-table.png)

<span data-ttu-id="77a26-536">*. Mdf* ve *. ldf* veritabanı dosyaları *\\ \<username> C:\Users* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="77a26-536">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="77a26-537">`EnsureCreated`Uygulama başlatma sırasında çalışan Başlatıcı metodunu çağırırken, artık sınıfta bir değişiklik yapabilir, `Student` veritabanını silebilir, uygulamayı yeniden çalıştırabilirsiniz ve veritabanı, değişikliklerinizi eşleştirmek için otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="77a26-537">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="77a26-538">Örneğin, `EmailAddress` sınıfa bir özellik eklerseniz `Student` , `EmailAddress` yeniden oluşturulan tabloda yeni bir sütun görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="77a26-538">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="77a26-539">Kurallar</span><span class="sxs-lookup"><span data-stu-id="77a26-539">Conventions</span></span>

<span data-ttu-id="77a26-540">Entity Framework, kuralların kullanımı veya Entity Framework varsayımlarıyla ilgili olarak en az bir veritabanı oluşturabilmesini sağlamak için yazmanız gereken kod miktarı.</span><span class="sxs-lookup"><span data-stu-id="77a26-540">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="77a26-541">`DbSet`Özelliklerin adları tablo adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-541">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="77a26-542">Bir özellik tarafından başvurulmayan varlıklar için `DbSet` , varlık sınıfı adları tablo adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-542">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="77a26-543">Varlık özelliği adları, sütun adları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-543">Entity property names are used for column names.</span></span>
* <span data-ttu-id="77a26-544">ID veya Classnameıd olarak adlandırılan varlık özellikleri, birincil anahtar özellikleri olarak tanınır.</span><span class="sxs-lookup"><span data-stu-id="77a26-544">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="77a26-545">Bir özellik, adlandırılmış ise yabancı anahtar özelliği olarak yorumlanır *\<navigation property name>\<primary key property name>* (örneğin, `StudentID` `Student` `Student` varlığın birincil anahtarı olduğundan gezinti özelliği için `ID` ).</span><span class="sxs-lookup"><span data-stu-id="77a26-545">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="77a26-546">Yabancı anahtar özellikleri de yalnızca adlandırılmış olabilir *\<primary key property name>* (örneğin, `EnrollmentID` `Enrollment` varlığın birincil anahtarı olduğundan `EnrollmentID` ).</span><span class="sxs-lookup"><span data-stu-id="77a26-546">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="77a26-547">Geleneksel davranış geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-547">Conventional behavior can be overridden.</span></span> <span data-ttu-id="77a26-548">Örneğin, bu öğreticide daha önce gördüğünüz gibi tablo adlarını açıkça belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-548">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="77a26-549">Ayrıca, bu serinin [sonraki bir öğreticide](complex-data-model.md) göreceğiniz gibi, sütun adlarını ayarlayabilir ve herhangi bir özelliği birincil anahtar veya yabancı anahtar olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a26-549">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="77a26-550">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="77a26-550">Asynchronous code</span></span>

<span data-ttu-id="77a26-551">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="77a26-551">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="77a26-552">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-552">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="77a26-553">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="77a26-553">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="77a26-554">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-554">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="77a26-555">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="77a26-555">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="77a26-556">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="77a26-556">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="77a26-557">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir, ancak düşük trafik durumlarında performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="77a26-557">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="77a26-558">Aşağıdaki kodda, `async` anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="77a26-558">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="77a26-559">`async`Anahtar sözcüğü, derleyiciye Yöntem gövdesinin parçaları için geri çağrılar oluşturmasını ve döndürülen nesneyi otomatik olarak oluşturmasını söyler `Task<IActionResult>` .</span><span class="sxs-lookup"><span data-stu-id="77a26-559">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="77a26-560">Dönüş türü, `Task<IActionResult>` türünde bir sonuçla devam eden işi temsil eder `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="77a26-560">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="77a26-561">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="77a26-561">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="77a26-562">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="77a26-562">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="77a26-563">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="77a26-563">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="77a26-564">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="77a26-564">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="77a26-565">Entity Framework kullanan zaman uyumsuz kod yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="77a26-565">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="77a26-566">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="77a26-566">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="77a26-567">Bu, örneğin,, `ToListAsync` ve içerir `SingleOrDefaultAsync` `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="77a26-567">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="77a26-568">Örneğin, gibi yalnızca bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="77a26-568">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="77a26-569">EF bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-569">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="77a26-570">Herhangi bir zaman uyumsuz EF yöntemini çağırdığınızda, her zaman `await` anahtar sözcüğünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="77a26-570">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="77a26-571">Zaman uyumsuz kodun performans avantajlarından yararlanmak isterseniz, kullanmakta olduğunuz tüm kitaplık paketlerinin (örneğin, sayfalama için), sorguların veritabanına gönderilmesine neden olan herhangi bir Entity Framework yöntemini çağırıyorsa, zaman uyumsuz olarak da kullanılmasını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="77a26-571">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="77a26-572">.NET ' te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [Async Overview](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="77a26-572">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="77a26-573">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="77a26-573">Next steps</span></span>

<span data-ttu-id="77a26-574">Temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerini nasıl gerçekleştireceğinizi öğrenmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="77a26-574">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="77a26-575">Temel CRUD işlevlerini uygulama</span><span class="sxs-lookup"><span data-stu-id="77a26-575">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
