---
title: 'Öğretici: ASP.NET bir MVC web uygulamasında EF Core ile başlayın'
description: Bu sıfırdan Contoso Üniversitesi örnek uygulama oluşturmak için nasıl açıklayan öğreticiler bir dizi ilkidir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: fca9fdc425506ec8b4eec5c609237208f4c0d7b5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511307"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="60b1a-103">Öğretici: ASP.NET bir MVC web uygulamasında EF Core ile başlayın</span><span class="sxs-lookup"><span data-stu-id="60b1a-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="60b1a-104">Bu öğretici Core 3.0 ASP.NET **güncelleştirilemedi.**</span><span class="sxs-lookup"><span data-stu-id="60b1a-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="60b1a-105">[Razor Pages sürümü](xref:data/ef-rp/intro) güncellendi.</span><span class="sxs-lookup"><span data-stu-id="60b1a-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="60b1a-106">Bu öğreticinin ASP.NET Core 3.0 ve sonraki sürümü için kod değişikliklerinin çoğu:</span><span class="sxs-lookup"><span data-stu-id="60b1a-106">Most of the code changes for the ASP.NET Core 3.0 and later version of this tutorial:</span></span>

* <span data-ttu-id="60b1a-107">*Startup.cs* ve *Program.cs* dosyalarında bulunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-107">Are in the *Startup.cs* and *Program.cs* files.</span></span>
* <span data-ttu-id="60b1a-108">[Razor Pages sürümünde](xref:data/ef-rp/intro)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-108">Can be found in the [Razor Pages version](xref:data/ef-rp/intro).</span></span> 

<span data-ttu-id="60b1a-109">Bunun ne zaman güncelleştirilebileceği hakkında bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/13920)bakın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-109">For information on when this might be updated, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="60b1a-110">Contoso Üniversitesi örnek web uygulaması, Entity Framework (EF) Core 2.2 ve Visual Studio 2017 veya 2019'u kullanarak ASP.NET Core 2.2 MVC web uygulamalarının nasıl oluşturulacak larını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-110">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="60b1a-111">Örnek uygulama kurgusal bir Contoso Üniversitesi için bir web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-111">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="60b1a-112">Öğrenci kabulü, ders oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-112">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="60b1a-113">Bu sıfırdan Contoso Üniversitesi örnek uygulama oluşturmak için nasıl açıklayan öğreticiler bir dizi ilkidir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-113">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="60b1a-114">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="60b1a-114">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="60b1a-115">ASP.NET Core MVC web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-115">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="60b1a-116">Site stilini ayarlama</span><span class="sxs-lookup"><span data-stu-id="60b1a-116">Set up the site style</span></span>
> * <span data-ttu-id="60b1a-117">EF Core NuGet paketleri hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="60b1a-117">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="60b1a-118">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-118">Create the data model</span></span>
> * <span data-ttu-id="60b1a-119">Veritabanı bağlamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-119">Create the database context</span></span>
> * <span data-ttu-id="60b1a-120">Bağımlılık enjeksiyonu için bağlamı kaydetme</span><span class="sxs-lookup"><span data-stu-id="60b1a-120">Register the context for dependency injection</span></span>
> * <span data-ttu-id="60b1a-121">Test verileriyle veritabanını başlatma</span><span class="sxs-lookup"><span data-stu-id="60b1a-121">Initialize the database with test data</span></span>
> * <span data-ttu-id="60b1a-122">Denetleyici ve görünümler oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-122">Create a controller and views</span></span>
> * <span data-ttu-id="60b1a-123">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="60b1a-123">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60b1a-124">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="60b1a-124">Prerequisites</span></span>

* [<span data-ttu-id="60b1a-125">.NET Çekirdek SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="60b1a-125">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="60b1a-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) aşağıdaki iş yükleri ile:</span><span class="sxs-lookup"><span data-stu-id="60b1a-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="60b1a-127">**ASP.NET ve web geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="60b1a-127">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="60b1a-128">**.NET Core çapraz platform geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="60b1a-128">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="60b1a-129">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="60b1a-129">Troubleshooting</span></span>

<span data-ttu-id="60b1a-130">Çözemediğiniz bir sorunla karşınıza çıkarsa, kodunuzu [tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)karşılaştırarak genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-130">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="60b1a-131">Sık karşılaşılan hataların listesi ve bunları nasıl çözeceğiniz için, [serinin son öğreticisinin Sorun Giderme bölümüne](advanced.md#common-errors)bakın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-131">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="60b1a-132">İhtiyacınız olanı bulamazsanız, [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya EF [Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için StackOverflow.com bir soru gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-132">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="60b1a-133">Bu 10 öğreticiler, her biri önceki öğreticiler yapılır ne üzerine inşa bir dizi.</span><span class="sxs-lookup"><span data-stu-id="60b1a-133">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="60b1a-134">Her başarılı öğretici tamamlandıktan sonra projenin bir kopyasını kaydetmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="60b1a-134">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="60b1a-135">Sonra sorunlarla karşınıza çıkarsa, tüm serinin başına geri dönmek yerine önceki öğretici baştan başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-135">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="60b1a-136">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="60b1a-136">Contoso University web app</span></span>

<span data-ttu-id="60b1a-137">Bu öğreticiler inşa olacak uygulama basit bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-137">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="60b1a-138">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-138">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="60b1a-139">Oluşturacağınız ekranlardan birkaçı aşağıda veda edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-139">Here are a few of the screens you'll create.</span></span>

![Öğrenci Endeksi sayfası](intro/_static/students-index.png)

![Öğrenciler Sayfayı Edit](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="60b1a-142">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-142">Create web app</span></span>

* <span data-ttu-id="60b1a-143">Visual Studio'yu açın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-143">Open Visual Studio.</span></span>

* <span data-ttu-id="60b1a-144">**Dosya** menüsünden **Yeni > Projesi'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-144">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="60b1a-145">Sol **bölmeden, Yüklü > Visual C# > Web'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-145">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="60b1a-146">ASP.NET **Çekirdek Web Uygulaması** proje şablonu seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-146">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="60b1a-147">Ad olarak **ContosoUniversity** girin ve **Tamam'ı**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-147">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Yeni Proje iletişim kutusu](intro/_static/new-project2.png)

* <span data-ttu-id="60b1a-149">**Yeni ASP.NET Çekirdek Web Uygulaması** iletişim kutusunun görünmesini bekleyin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-149">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="60b1a-150">**.NET Core**, **ASP.NET Core 2.2** ve **Web Application (Model-View-Controller)** şablonu'nu seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-150">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="60b1a-151">Kimlik **Doğrulamanın** Kimlik **Doğrulama Yok**olarak ayarlandıklarına emin olun.</span><span class="sxs-lookup"><span data-stu-id="60b1a-151">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="60b1a-152">**Tamam'ı** seçin</span><span class="sxs-lookup"><span data-stu-id="60b1a-152">Select **OK**</span></span>

  ![Yeni ASP.NET Çekirdek Projesi iletişim kutusu](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="60b1a-154">Site stilini ayarlama</span><span class="sxs-lookup"><span data-stu-id="60b1a-154">Set up the site style</span></span>

<span data-ttu-id="60b1a-155">Birkaç basit değişiklik site menüsünü, düzeni ve ana sayfayı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-155">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="60b1a-156">*Görünümleri Aç/Paylaşılan/_Layout.cshtml* ve aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="60b1a-156">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="60b1a-157">"ContosoUniversity"in her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-157">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="60b1a-158">Üç olay var.</span><span class="sxs-lookup"><span data-stu-id="60b1a-158">There are three occurrences.</span></span>

* <span data-ttu-id="60b1a-159">**Hakkında**, **Öğrenciler**, **Kurslar**, **Eğitmenler**ve **Bölümler**için menü girişleri ekleyin ve **Gizlilik** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-159">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="60b1a-160">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-160">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="60b1a-161">*Görünümler/Ana Sayfa/Index.cshtml'de,* ASP.NET ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="60b1a-161">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="60b1a-162">Projeyi çalıştırmak için CTRL+F5 tuşuna basın veya menüden **Hata Ayıklama olmadan Başlat > Hata Ayıklama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-162">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="60b1a-163">Bu öğreticilerde oluşturacağınız sayfaların sekmelerini içeren ana sayfayı görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-163">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso Üniversitesi giriş sayfası](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="60b1a-165">EF Core NuGet paketleri hakkında</span><span class="sxs-lookup"><span data-stu-id="60b1a-165">About EF Core NuGet packages</span></span>

<span data-ttu-id="60b1a-166">Projeye EF Core desteği eklemek için hedeflemek istediğiniz veritabanı sağlayıcısını yükleyin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-166">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="60b1a-167">Bu öğretici SQL Server kullanır ve sağlayıcı paketi [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)olduğunu.</span><span class="sxs-lookup"><span data-stu-id="60b1a-167">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="60b1a-168">Bu paket [Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app)dahildir, bu nedenle pakete başvurmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="60b1a-168">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="60b1a-169">EF SQL Server paketi`Microsoft.EntityFrameworkCore` ve bağımlılıkları ( ve `Microsoft.EntityFrameworkCore.Relational`) EF için çalışma zamanı desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-169">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="60b1a-170">Daha [sonra, Geçişler](migrations.md) öğreticisinde bir araç paketi eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-170">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="60b1a-171">Entity Framework Core için kullanılabilen diğer veritabanı sağlayıcıları hakkında bilgi için veritabanı [sağlayıcılarına](/ef/core/providers/)bakın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-171">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="60b1a-172">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-172">Create the data model</span></span>

<span data-ttu-id="60b1a-173">Daha sonra Contoso Üniversitesi uygulaması için varlık sınıfları oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="60b1a-173">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="60b1a-174">Aşağıdaki üç varlıkla başlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="60b1a-174">You'll start with the following three entities.</span></span>

![Ders-Kayıt-Öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="60b1a-176">Varlıklar `Student` la `Enrollment` varlıklar arasında bir-çok ilişkisi vardır ve varlıklarla varlıklar arasında `Course` `Enrollment` bire bir ilişki vardır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-176">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="60b1a-177">Başka bir deyişle, bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir derse herhangi bir sayıda öğrenci kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-177">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="60b1a-178">Aşağıdaki bölümlerde bu varlıkların her biri için bir sınıf oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-178">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="60b1a-179">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="60b1a-179">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="60b1a-181">*Modeller* klasöründe, *Student.cs* adında bir sınıf dosyası oluşturun ve şablon kodunu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-181">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="60b1a-182">Özellik, `ID` veritabanı tablosunun bu sınıfa karşılık gelen birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-182">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="60b1a-183">Varsayılan olarak Entity Framework, `ID` veya `classnameID` olarak adlandırılan özellikleri birincil anahtar olarak algılar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-183">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="60b1a-184">Özellik `Enrollments` bir [navigasyon özelliğidir.](/ef/core/modeling/relationships)</span><span class="sxs-lookup"><span data-stu-id="60b1a-184">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="60b1a-185">Gezinti özellikleri, bu varlıkla ilişkili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-185">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="60b1a-186">Bu durumda, `Enrollments` a'nın `Student entity` özelliği, o `Enrollment` `Student` varlıkla ilişkili tüm varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-186">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="60b1a-187">Başka bir deyişle, veritabanındaki belirli bir Öğrenci satırında ilgili iki Kayıt satırı varsa (öğrencinin Öğrenci Kimliği yabancı `Student` anahtar sütunundaki birincil anahtar değerini içeren satırlar), bu varlığın `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-187">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="60b1a-188">Bir gezinti özelliği birden çok tüzel kişilik tutabiliyorsa (çok-çok veya bir-çok ilişkisinde olduğu gibi), türü girişlerin `ICollection<T>`eklenebileceği, silinebileceği ve güncelleştirilebildiği bir liste olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-188">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="60b1a-189">Belirtebilirsiniz `ICollection<T>` veya gibi bir `List<T>` `HashSet<T>`tür ya da .</span><span class="sxs-lookup"><span data-stu-id="60b1a-189">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="60b1a-190">Belirtirseniz, `ICollection<T>`EF varsayılan `HashSet<T>` olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-190">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="60b1a-191">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="60b1a-191">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="60b1a-193">*Modeller* klasöründe, *Enrollment.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="60b1a-193">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="60b1a-194">Özellik `EnrollmentID` birincil anahtar olacak; bu varlık, `classnameID` `Student` varlıkta `ID` gördüğünüz gibi kendi başına değil deseni kullanır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-194">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="60b1a-195">Normalde bir desen seçer ve veri modeli boyunca kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-195">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="60b1a-196">Burada, varyasyon her iki deseni de kullanabileceğinizi göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-196">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="60b1a-197">Daha [sonraki](inheritance.md)bir öğreticide, sınıf adı olmadan kimlik kullanmanın veri modelinde devralmayı nasıl kolaylaştırdığını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-197">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="60b1a-198">Özellik `Grade` bir `enum`.</span><span class="sxs-lookup"><span data-stu-id="60b1a-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="60b1a-199">Tür bildiriminden `Grade` sonraki soru işareti `Grade` özelliğin geçersiz olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="60b1a-200">Sıfır notundan farklı olan bir not - null, bir notun bilinmediği veya henüz atanmadığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-200">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="60b1a-201">Özellik `StudentID` yabancı bir anahtardır ve ilgili `Student`navigasyon özelliği .</span><span class="sxs-lookup"><span data-stu-id="60b1a-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="60b1a-202">Bir `Enrollment` varlık tek `Student` bir varlıkla ilişkilidir, bu `Student` nedenle özellik `Student.Enrollments` yalnızca tek bir varlığı tutabilir `Enrollment` (daha önce gördüğünüz ve birden çok varlığı tutabilen gezinti özelliğinden farklı olarak).</span><span class="sxs-lookup"><span data-stu-id="60b1a-202">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="60b1a-203">Özellik `CourseID` yabancı bir anahtardır ve ilgili `Course`navigasyon özelliği .</span><span class="sxs-lookup"><span data-stu-id="60b1a-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="60b1a-204">Bir `Enrollment` varlık tek `Course` bir varlıkla ilişkilidir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="60b1a-205">Entity Framework, bir özelliği, `<navigation property name><primary key property name>` adlandırılmışsa yabancı bir anahtar `StudentID` özelliği `Student` olarak yorumlar `Student` (örneğin, varlığın birincil anahtarı olduğundan `ID`gezinti özelliği için).</span><span class="sxs-lookup"><span data-stu-id="60b1a-205">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="60b1a-206">Yabancı anahtar özellikleri de `<primary key property name>` basitçe adlandırılabilir `CourseID` (örneğin, varlığın `Course` `CourseID`birincil anahtarı olduğundan).</span><span class="sxs-lookup"><span data-stu-id="60b1a-206">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="60b1a-207">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="60b1a-207">The Course entity</span></span>

![Ders varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="60b1a-209">*Modeller* klasöründe, *Course.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="60b1a-209">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="60b1a-210">Tesis `Enrollments` bir navigasyon özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-210">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="60b1a-211">Bir `Course` varlık herhangi bir sayıda `Enrollment` varlıkla ilişkili olabilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-211">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="60b1a-212">Biz bu serinin `DatabaseGenerated` daha sonraki bir [öğretici](complex-data-model.md) öznitelik hakkında daha fazla şey söyleyeceğiz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-212">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="60b1a-213">Temel olarak, bu öznitelik, veritabanının oluşturmasıyerine kursun birincil anahtarını girmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-213">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="60b1a-214">Veritabanı bağlamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-214">Create the database context</span></span>

<span data-ttu-id="60b1a-215">Belirli bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıf veritabanı bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-215">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="60b1a-216">Bu sınıfı `Microsoft.EntityFrameworkCore.DbContext` sınıftan çıkararak oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-216">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="60b1a-217">Kodunuzda veri modeline hangi varlıkların dahil olduğunu belirtirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-217">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="60b1a-218">Belirli Varlık Çerçevesi davranışını da özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-218">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="60b1a-219">Bu projede sınıfadlandırılmış. `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="60b1a-219">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="60b1a-220">Proje *klasöründe, Veri*adlı bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="60b1a-220">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="60b1a-221">*Veri* klasöründe *SchoolContext.cs*adında yeni bir sınıf dosyası oluşturun ve şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="60b1a-221">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="60b1a-222">Bu kod, `DbSet` her varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-222">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="60b1a-223">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-223">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="60b1a-224">İfadeleri atlayabilirdin `DbSet<Enrollment>` `DbSet<Course>` ve aynı şekilde işe yarayacak.</span><span class="sxs-lookup"><span data-stu-id="60b1a-224">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="60b1a-225">`Student` Varlık, `Enrollment` varlık referansları ve `Enrollment` varlık varlık referansları `Course` nedeniyle Varlık Çerçevesi bunları dolaylı olarak içerecektir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-225">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="60b1a-226">Veritabanı oluşturulduğunda, EF `DbSet` özellik adlarıyla aynı adlara sahip tablolar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-226">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="60b1a-227">Koleksiyonlar için özellik adları genellikle çoğuldur (Öğrenci yerine Öğrenciler), ancak geliştiriciler tablo adlarının çoğul olarak verilip verilmeyeceği konusunda hemfikir değildir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-227">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="60b1a-228">Bu öğreticiler için, DbContext'da tekil tablo adlarını belirterek varsayılan davranışı geçersiz kılarsınız.</span><span class="sxs-lookup"><span data-stu-id="60b1a-228">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="60b1a-229">Bunu yapmak için, son DbSet özelliğinden sonra aşağıdaki vurgulanan kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="60b1a-230">Okul Bağlamını Kaydedin</span><span class="sxs-lookup"><span data-stu-id="60b1a-230">Register the SchoolContext</span></span>

<span data-ttu-id="60b1a-231">ASP.NET Core varsayılan olarak [bağımlılık enjeksiyonuyguluyor.](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="60b1a-231">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="60b1a-232">Hizmetler (EF veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-232">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="60b1a-233">Bu hizmetleri gerektiren bileşenlere (MVC denetleyicileri gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-233">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="60b1a-234">Bu öğreticide daha sonra bir bağlam örneği alan denetleyici oluşturucu kodunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-234">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="60b1a-235">Hizmet `SchoolContext` olarak kaydolmak için *Startup.cs*açın ve vurgulanan `ConfigureServices` satırları yönteme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-235">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="60b1a-236">Bağlantı dizesinin adı, `DbContextOptionsBuilder` bir nesne üzerinde bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="60b1a-237">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="60b1a-238">`ContosoUniversity.Data` Ekstreler `Microsoft.EntityFrameworkCore` ve ad alanları ekleyin `using` ve sonra projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="60b1a-238">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="60b1a-239">*appsettings.json* dosyasını açın ve aşağıdaki örnekte gösterildiği gibi bir bağlantı dizesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-239">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="60b1a-240">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="60b1a-240">SQL Server Express LocalDB</span></span>

<span data-ttu-id="60b1a-241">Bağlantı dizesi bir SQL Server LocalDB veritabanı belirtir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-241">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="60b1a-242">LocalDB, SQL Server Express Veritabanı Altyapısının hafif bir sürümüdür ve üretim kullanımı için değil, uygulama geliştirme için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-242">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="60b1a-243">LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-243">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="60b1a-244">Varsayılan olarak, LocalDB `C:/Users/<user>` dizinde *.mdf* veritabanı dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-244">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="60b1a-245">Test verileriyle DB'yi başlatma</span><span class="sxs-lookup"><span data-stu-id="60b1a-245">Initialize DB with test data</span></span>

<span data-ttu-id="60b1a-246">Varlık Çerçevesi sizin için boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-246">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="60b1a-247">Bu bölümde, veritabanı test verileriyle doldurmak için oluşturulduktan sonra çağrılan bir yöntem yazarsınız.</span><span class="sxs-lookup"><span data-stu-id="60b1a-247">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="60b1a-248">Burada veritabanını `EnsureCreated` otomatik olarak oluşturmak için yöntemi kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="60b1a-248">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="60b1a-249">Daha [sonraki](migrations.md) bir öğreticide, veritabanını bırakmak ve yeniden oluşturmak yerine veritabanı şemasını değiştirmek için Kod İlk Geçişleri'ni kullanarak model değişikliklerini nasıl işleyeceğiniz göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-249">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="60b1a-250">*Veri* klasöründe, *DbInitializer.cs* adında yeni bir sınıf dosyası oluşturun ve gerektiğinde bir veritabanıoluşturulmasına neden olan ve test verilerini yeni veritabanına yükleyen şablon kodunu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-250">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="60b1a-251">Kod, veritabanında öğrenci olup olmadığını denetler ve varsa, veritabanının yeni olduğunu ve test verileriyle tohumlanması gerektiğini varsayar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-251">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="60b1a-252">Performansı en iyi duruma getirmek `List<T>` için test verilerini koleksiyonlar yerine dizilere yükler.</span><span class="sxs-lookup"><span data-stu-id="60b1a-252">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="60b1a-253">*Program.cs,* uygulama `Main` başlatma da aşağıdaki leri yapmak için yöntemi değiştirin:</span><span class="sxs-lookup"><span data-stu-id="60b1a-253">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="60b1a-254">Bağımlılık enjeksiyon kapsayıcısından bir veritabanı bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-254">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="60b1a-255">Tohum yöntemini çağırın, ona bağlamı geçirin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-255">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="60b1a-256">Tohum yöntemi yapıldığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-256">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="60b1a-257">Deyim `using` ler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="60b1a-257">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="60b1a-258">Eski öğreticilerde, `Configure` *Startup.cs*yönteminde benzer kod görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-258">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="60b1a-259">`Configure` Yöntemi yalnızca istek ardışık hattını ayarlamak için kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-259">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="60b1a-260">Uygulama başlangıç kodu `Main` yönteme aittir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-260">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="60b1a-261">Şimdi uygulamayı ilk çalıştırdığınızda, veritabanı oluşturulur ve test verileriyle tohumlanır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-261">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="60b1a-262">Veri modelinizi her değiştirdiğinizde, veritabanını silebilir, tohum yönteminizi güncelleyebilir ve aynı şekilde yeni bir veritabanıyla yeniden başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-262">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="60b1a-263">Daha sonraki öğreticilerde, veri modeli değiştiğinde veritabanını silmeden ve yeniden oluşturmadan nasıl değiştireceğinizi görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-263">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="60b1a-264">Denetleyici ve görünümler oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-264">Create controller and views</span></span>

<span data-ttu-id="60b1a-265">Ardından, Visual Studio'daki iskele altyapısını kullanarak bir MVC denetleyicisi ve verileri sorgulamak ve kaydetmek için EF'yi kullanacak görünümler eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-265">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="60b1a-266">CRUD eylem yöntemleri ve görünümleri otomatik oluşturma iskele olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-266">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="60b1a-267">İskele, iskele kodun kendi gereksinimlerinize uyacak şekilde değiştirebileceğiniz bir başlangıç noktası olması nedeniyle kod oluşturma noktasından farklıdır, oysa genellikle oluşturulan kodu değiştirmezsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-267">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="60b1a-268">Oluşturulan kodu özelleştirmeniz gerektiğinde, kısmi sınıflar kullanırsınız veya bir şeyler değiştiğinde kodu yeniden oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-268">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="60b1a-269">**Solution Explorer'daki** **Denetleyiciler** klasörüne sağ tıklayın ve Yeni İskele Öğesi **> ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-269">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="60b1a-270">İskele **Ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="60b1a-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="60b1a-271">**Entity Framework'u kullanarak görünüme sahip MVC denetleyicisi**seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-271">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="60b1a-272">**Ekle**’ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-272">Click **Add**.</span></span> <span data-ttu-id="60b1a-273">**Görünümli MVC Denetleyicisi Ekle, Entity Framework** iletişim kutusunu kullanarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![İskele Öğrenci](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="60b1a-275">**Model sınıfında** **Öğrenci**seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-275">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="60b1a-276">**Veri bağlamında sınıf** seçin **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="60b1a-276">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="60b1a-277">Varsayılan **StudentsController'ı** ad olarak kabul edin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="60b1a-278">**Ekle**’ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-278">Click **Add**.</span></span>

  <span data-ttu-id="60b1a-279">**Ekle'yi**tıklattığınızda, Visual Studio iskele motoru *denetleyiciyle* çalışan bir StudentsController.cs dosyası ve bir görünüm kümesi *(.cshtml* dosyaları) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-279">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="60b1a-280">(İskele motoru, bu öğretici için daha önce yaptığınız gibi ilk el ile oluşturmazsanız, veritabanı bağlamını da sizin için oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="60b1a-281">**Veri bağlamı sınıfının**sağındaki artı işaretini tıklatarak **Denetleyici Ekle** kutusunda yeni bir bağlam sınıfı belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="60b1a-282">Visual Studio daha `DbContext` sonra sınıfınızın yanı sıra denetleyici ve görünümler oluşturacaktır.)</span><span class="sxs-lookup"><span data-stu-id="60b1a-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="60b1a-283">Denetleyicinin bir `SchoolContext` oluşturucu parametre olarak aldığını fark edeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="60b1a-284">ASP.NET Çekirdek bağımlılık enjeksiyonu denetleyici `SchoolContext` içine bir örnek geçen ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="60b1a-285">Bunu daha önce *Startup.cs* dosyasında yapılandırmışsınız.</span><span class="sxs-lookup"><span data-stu-id="60b1a-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="60b1a-286">Denetleyici, veritabanındaki tüm öğrencileri görüntüleyen bir `Index` eylem yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="60b1a-287">Yöntem, veritabanı bağlamı örneğinin özelliğini `Students` okuyarak Öğrenci varlığından belirlenen öğrencilerin listesini alır:</span><span class="sxs-lookup"><span data-stu-id="60b1a-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="60b1a-288">Bu koddaki eşzamanlı programlama öğeleri hakkında daha sonra öğreticide öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="60b1a-289">*Görünümler/Öğrenciler/Index.cshtml* görünümü bu listeyi bir tabloda görüntüler:</span><span class="sxs-lookup"><span data-stu-id="60b1a-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="60b1a-290">Projeyi çalıştırmak için CTRL+F5 tuşuna basın veya menüden **Hata Ayıklama olmadan Başlat > Hata Ayıklama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="60b1a-291">Yöntemin ekteki test verilerini görmek `DbInitializer.Initialize` için Öğrenciler sekmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="60b1a-292">Tarayıcı pencerenizin ne kadar dar olduğuna bağlı `Students` olarak, sayfanın üst kısmındaki sekme bağlantısını görürsünüz veya bağlantıyı görmek için sağ üst köşedeki gezinti simgesini tıklatmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-292">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso Üniversitesi giriş sayfası dar](intro/_static/home-page-narrow.png)

![Öğrenci Endeksi sayfası](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="60b1a-295">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="60b1a-295">View the database</span></span>

<span data-ttu-id="60b1a-296">Uygulamayı başlattığınızda, `DbInitializer.Initialize` yöntem çağırır. `EnsureCreated`</span><span class="sxs-lookup"><span data-stu-id="60b1a-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="60b1a-297">EF hiçbir veritabanı olduğunu gördüm ve bu yüzden bir `Initialize` oluşturdu, sonra yöntem kodunun geri kalanı veri ile veritabanı doldurulur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="60b1a-298">Visual Studio'daki veritabanını görüntülemek için **SQL Server Object Explorer'ı** (SSOX) kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="60b1a-299">Tarayıcıyı kapatın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-299">Close the browser.</span></span>

<span data-ttu-id="60b1a-300">SSOX penceresi zaten açık değilse, Visual Studio'daki **Görünüm** menüsünden seçin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="60b1a-301">SSOX'ta **(localdb)\MSSQLLocalDB > Databases'ı**tıklatın ve ardından *appsettings.json* dosyanızdaki bağlantı dizesinde bulunan veritabanı adının girişini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="60b1a-302">Veritabanınızdaki tabloları görmek için **Tablolar** düğümunu genişletin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-302">Expand the **Tables** node to see the tables in your database.</span></span>

![SSOX tabloları](intro/_static/ssox-tables.png)

<span data-ttu-id="60b1a-304">Oluşturulan sütunları ve tabloya eklenen satırları görmek için **Öğrenci** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX öğrenci tablosu](intro/_static/ssox-student-table.png)

<span data-ttu-id="60b1a-306">*.mdf* ve *.ldf* veritabanı dosyaları *C:\Users\\\<yourusername>* klasöründe bulunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-306">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="60b1a-307">Uygulama başlangıcında çalışan `EnsureCreated` başlatım yöntemini aradığınızdan, artık `Student` sınıfta bir değişiklik yapabilir, veritabanını silebilir, uygulamayı yeniden çalıştırabilirsiniz ve veritabanı değişikliğinize uyacak şekilde otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="60b1a-308">Örneğin, `EmailAddress` `Student` sınıfa bir özellik eklerseniz, yeniden oluşturulan `EmailAddress` tabloda yeni bir sütun görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="60b1a-309">Kurallar</span><span class="sxs-lookup"><span data-stu-id="60b1a-309">Conventions</span></span>

<span data-ttu-id="60b1a-310">Varlık Çerçevesi'nin sizin için tam bir veritabanı oluşturabilmesi için yazmanız gereken kod miktarı, Varlık Çerçevesi'nin yaptığı kuralların veya varsayımların kullanımı nedeniyle çok azdır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="60b1a-311">Özelliklerin `DbSet` adları tablo adları olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="60b1a-312">Bir `DbSet` özellik tarafından başvurulmayan varlıklar için, varlık sınıf adları tablo adları olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="60b1a-313">Sütun adları için varlık özellik adları kullanılır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="60b1a-314">ID veya classnameID adlı varlık özellikleri birincil anahtar özellikleri olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="60b1a-315">Bir özellik, \* \<gezinti özelliği adı \<>birincil anahtar özellik adı>\* (örneğin, `StudentID` `Student` `Student` varlığın birincil anahtarı olduğundan `ID`gezinti özelliği için) adlı yabancı anahtar özelliği olarak yorumlanır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-315">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="60b1a-316">Yabancı anahtar özellikleri de `Enrollment` yalnızca `EnrollmentID` \* \<birincil anahtar özellik adı>\* adlandırılabilir (örneğin, `EnrollmentID` varlığın birincil anahtarı olduğundan).</span><span class="sxs-lookup"><span data-stu-id="60b1a-316">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="60b1a-317">Geleneksel davranışlar geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="60b1a-318">Örneğin, bu öğreticide daha önce gördüğünüz gibi tablo adlarını açıkça belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="60b1a-319">Sütun adlarını ayarlayabilir ve bu serinin daha sonraki bir [öğreticisinde](complex-data-model.md) göreceğiniz gibi, herhangi bir özelliği birincil anahtar veya yabancı anahtar olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="60b1a-320">Asynchronous kodu</span><span class="sxs-lookup"><span data-stu-id="60b1a-320">Asynchronous code</span></span>

<span data-ttu-id="60b1a-321">Asynchronous programlama ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="60b1a-322">Bir web sunucusunda sınırlı sayıda iş parçacığı vardır ve yüksek yük durumlarında kullanılabilir tüm iş parçacıkları kullanılıyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="60b1a-323">Bu durumda, iş parçacıkları serbest bırakılıncaya kadar sunucu yeni istekleri işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="60b1a-324">Senkron kodla, G/Ç'nin tamamlanmasını bekledikleri için birçok iş parçacığı aslında herhangi bir iş yapmasalar da bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="60b1a-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="60b1a-325">Bir işlem G/Ç'nin tamamlanmasını beklerken, iş parçacığı sunucunun diğer istekleri işlemek için kullanması için serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="60b1a-326">Sonuç olarak, eşzamanlı kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucunun gecikmeden daha fazla trafiği işlemesi sağlanır.</span><span class="sxs-lookup"><span data-stu-id="60b1a-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="60b1a-327">Asynchronous kodu çalışma zamanında ek yükü küçük bir miktar tanıtmak yok, ancak düşük trafik durumları için performans isabet ihmal edilebilir, yüksek trafik durumları için ise, potansiyel performans geliştirme önemli.</span><span class="sxs-lookup"><span data-stu-id="60b1a-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="60b1a-328">Aşağıdaki kodda, `async` anahtar kelime, `Task<T>` iade `await` değeri, `ToListAsync` anahtar kelime ve yöntem kodu eşzamanlı olarak yürütmeyi yapar.</span><span class="sxs-lookup"><span data-stu-id="60b1a-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="60b1a-329">Anahtar `async` kelime derleyiciye yöntem gövdesinin bölümleri için geri aramalar oluşturmasını `Task<IActionResult>` ve döndürülen nesneyi otomatik olarak oluşturmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="60b1a-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="60b1a-330">İade türü, `Task<IActionResult>` türünden `IActionResult`bir sonucu olan devam eden çalışmayı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="60b1a-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="60b1a-331">Anahtar `await` kelime derleyicinin yöntemi iki bölüme bölmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="60b1a-332">İlk bölüm, eş zamanlı olarak başlatılan işlemle sona erer.</span><span class="sxs-lookup"><span data-stu-id="60b1a-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="60b1a-333">İkinci bölüm, işlem tamamlandığında çağrılan bir geri arama yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="60b1a-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="60b1a-334">`ToListAsync`uzantı yönteminin `ToList` eşzamanlı sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="60b1a-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="60b1a-335">Varlık Çerçevesi'ni kullanan eşzamanlı kod yazarken dikkat edilmesi gereken bazı noktalar:</span><span class="sxs-lookup"><span data-stu-id="60b1a-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="60b1a-336">Yalnızca sorguların veya komutların veritabanına gönderilmesine neden olan ifadeler eşsenkronize olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="60b1a-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="60b1a-337">Buna, örneğin, `ToListAsync`, `SingleOrDefaultAsync`, `SaveChangesAsync`ve .</span><span class="sxs-lookup"><span data-stu-id="60b1a-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="60b1a-338">Örneğin, bir `IQueryable`şeyi değiştiren ifadeler `var students = context.Students.Where(s => s.LastName == "Davolio")`içermez.</span><span class="sxs-lookup"><span data-stu-id="60b1a-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="60b1a-339">EF bağlamı iş parçacığı güvenli değildir: paralel olarak birden çok işlem yapmaya çalışmayın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="60b1a-340">Herhangi bir async EF yöntemini `await` çağırdığınızda, her zaman anahtar kelimeyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="60b1a-341">Async kodunun performans avantajlarından yararlanmak istiyorsanız, kullandığınız kitaplık paketlerinin (sayfa lama gibi) sorguların veritabanına gönderilmesine neden olan Varlık Çerçevesi yöntemlerini aramaları durumunda da async kullandığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="60b1a-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="60b1a-342">.NET'teki eşzamanlı programlama hakkında daha fazla bilgi için [Async Genel Bakış'a](/dotnet/articles/standard/async)bakın.</span><span class="sxs-lookup"><span data-stu-id="60b1a-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="60b1a-343">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="60b1a-343">Get the code</span></span>

[<span data-ttu-id="60b1a-344">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-344">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="60b1a-345">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="60b1a-345">Next steps</span></span>

<span data-ttu-id="60b1a-346">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="60b1a-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="60b1a-347">Core MVC web uygulaması ASP.NET oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="60b1a-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="60b1a-348">Site stilini ayarlama</span><span class="sxs-lookup"><span data-stu-id="60b1a-348">Set up the site style</span></span>
> * <span data-ttu-id="60b1a-349">EF Core NuGet paketleri hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="60b1a-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="60b1a-350">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-350">Created the data model</span></span>
> * <span data-ttu-id="60b1a-351">Veritabanı bağlamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="60b1a-351">Created the database context</span></span>
> * <span data-ttu-id="60b1a-352">Okul Bağlamını Kaydetti</span><span class="sxs-lookup"><span data-stu-id="60b1a-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="60b1a-353">Test verileri ile başharfe çevrilmiş DB</span><span class="sxs-lookup"><span data-stu-id="60b1a-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="60b1a-354">Oluşturulan denetleyici ve görünümler</span><span class="sxs-lookup"><span data-stu-id="60b1a-354">Created controller and views</span></span>
> * <span data-ttu-id="60b1a-355">Veritabanı görüntülendi</span><span class="sxs-lookup"><span data-stu-id="60b1a-355">Viewed the database</span></span>

<span data-ttu-id="60b1a-356">Aşağıdaki öğreticide, temel CRUD (oluşturma, okuma, güncelleme, silme) işlemlerini nasıl yapacağınızı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="60b1a-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="60b1a-357">Temel CRUD (oluşturma, okuma, güncelleme, silme) işlemlerinin nasıl yapılacağını öğrenmek için bir sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="60b1a-357">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="60b1a-358">Temel CRUD işlevselliğini uygulayın</span><span class="sxs-lookup"><span data-stu-id="60b1a-358">Implement basic CRUD functionality</span></span>](crud.md)

