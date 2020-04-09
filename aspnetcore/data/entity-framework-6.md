---
title: ASP.NET Çekirdek ve Varlık Çerçevesi 6 ile Başlayın
author: rick-anderson
description: Bu makalede, ASP.NET Core uygulamasında Entity Framework 6 nasıl kullanılacağı gösterilmektedir.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656389"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="8ef1d-103">ASP.NET Çekirdek ve Varlık Çerçevesi 6 ile Başlayın</span><span class="sxs-lookup"><span data-stu-id="8ef1d-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="8ef1d-104">Yazar: [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8ef1d-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8ef1d-105">Bu makalede, ASP.NET Core uygulamasında Entity Framework 6 nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="8ef1d-106">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="8ef1d-106">Overview</span></span>

<span data-ttu-id="8ef1d-107">Entity Framework 6'yı kullanmak için, Entity Framework 6 .NET Core'u desteklemediği için projenizin .NET Framework'e karşı derlemesi vardır.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="8ef1d-108">Platform ötesi özelliklere ihtiyacınız varsa [Entity Framework Core'a](/ef/)yükseltmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="8ef1d-109">Varlık Framework 6'yı ASP.NET Core uygulamasında kullanmanın önerilen yolu, EF6 bağlamını ve model sınıflarını .NET Framework'ü hedefleyen bir sınıf kitaplığı projesine koymaktır.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="8ef1d-110">ASP.NET Core projesinden sınıf kitaplığına bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="8ef1d-111">[EF6 ve ASP.NET Core projeleri ile](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)örnek Visual Studio çözümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="8ef1d-112">bir ASP.NET Çekirdek projesine BIR EF6 bağlamı koyamazsınız, çünkü .NET Core *projeleri, Geçişleri Etkinleştir* gibi EF6 komutlarının gerektirdiği tüm işlevleri desteklemez.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="8ef1d-113">EF6 bağlamınızı hangi proje türünden bulursanız bulun, yalnızca EF6 komut satırı araçları bir EF6 bağlamıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="8ef1d-114">Örneğin, `Scaffold-DbContext` yalnızca Entity Framework Core'da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="8ef1d-115">Bir veritabanının bir EF6 modeline ters mühendislik yapması gerekiyorsa, [Önce Varolan Veritabanına Kod'u](https://msdn.microsoft.com/jj200620)bakın.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="8ef1d-116">ASP.NET Core projesinde referans tam çerçeve ve EF6</span><span class="sxs-lookup"><span data-stu-id="8ef1d-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="8ef1d-117">ASP.NET Core projenizin .NET Framework'u hedeflemesi ve EF6'ya başvurması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-117">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="8ef1d-118">Örneğin, ASP.NET Core projenizin *.csproj* dosyası aşağıdaki örneğe benzer (yalnızca dosyanın ilgili bölümleri gösterilir).</span><span class="sxs-lookup"><span data-stu-id="8ef1d-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="8ef1d-119">Yeni bir proje **oluştururken, ASP.NET Çekirdek Web Uygulaması (.NET Framework)** şablonundan yararlanın.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="8ef1d-120">Bağlantı dizelerini işleme</span><span class="sxs-lookup"><span data-stu-id="8ef1d-120">Handle connection strings</span></span>

<span data-ttu-id="8ef1d-121">EF6 sınıf kitaplığı projesinde kullanacağınız EF6 komut satırı araçları, bağlamı anında oluşturabilmeleri için varsayılan bir oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="8ef1d-122">Ancak büyük olasılıkla ASP.NET Çekirdek projesinde kullanılacak bağlantı dizesini belirtmek istersiniz, bu durumda bağlam oluşturucunuz bağlantı dizesini geçmenizi sağlayan bir parametreye sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="8ef1d-123">Bir örneği aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="8ef1d-124">EF6 bağlamınızda parametresiz bir oluşturucu olmadığından, EF6 projenizin [IDbContextFactory](https://msdn.microsoft.com/library/hh506876)uygulamasını sağlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="8ef1d-125">EF6 komut satırı araçları, bağlamı anında kullanabilmeleri için bu uygulamayı bulur ve kullanır.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="8ef1d-126">Bir örneği aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="8ef1d-127">Bu örnek kodda, `IDbContextFactory` uygulama sabit kodlu bir bağlantı dizesinde geçer.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="8ef1d-128">Bu, komut satırı araçlarının kullanacağı bağlantı dizesidir.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="8ef1d-129">Sınıf kitaplığı arama uygulamasının kullandığı bağlantı dizesini kullandığından emin olmak için bir strateji uygulamak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="8ef1d-130">Örneğin, her iki projede de bir ortam değişkeninden değer alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="8ef1d-131">ASP.NET Core projesinde bağımlılık enjeksiyonu ayarlama</span><span class="sxs-lookup"><span data-stu-id="8ef1d-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="8ef1d-132">Core projesinin *Startup.cs* dosyasında, bağımlılık enjeksiyonu (DI) `ConfigureServices`için EF6 bağlamını .</span><span class="sxs-lookup"><span data-stu-id="8ef1d-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="8ef1d-133">EF bağlam nesneleri, istek başına bir yaşam süresi için kapsamalmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="8ef1d-134">Daha sonra DI kullanarak denetleyicileri bağlamında bir örnek alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="8ef1d-135">Kod, EF Core bağlamı için yazdıklarına benzer:</span><span class="sxs-lookup"><span data-stu-id="8ef1d-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="8ef1d-136">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="8ef1d-136">Sample application</span></span>

<span data-ttu-id="8ef1d-137">Çalışan bir örnek uygulama için, bu makaleye eşlik eden [örnek Visual Studio çözümüne](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) bakın.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="8ef1d-138">Bu örnek Visual Studio'da aşağıdaki adımlarla sıfırdan oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="8ef1d-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="8ef1d-139">Bir çözüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-139">Create a solution.</span></span>

* <span data-ttu-id="8ef1d-140">**Add** > **Yeni Proje** > **Web** > **ASP.NET Çekirdek Web Uygulaması** Ekle</span><span class="sxs-lookup"><span data-stu-id="8ef1d-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="8ef1d-141">Proje şablonu seçim iletişim kutusunda, açılır açılır durumda API ve .NET Framework'u seçin</span><span class="sxs-lookup"><span data-stu-id="8ef1d-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="8ef1d-142">**Add** > **Yeni Proje** > **Ekle Windows Masaüstü** > **Sınıf Kitaplığı (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="8ef1d-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="8ef1d-143">Her iki proje için **paket yöneticisi konsolunda** `Install-Package Entityframework`(PMC) komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="8ef1d-144">Sınıf kitaplığı projesinde, veri modeli sınıfları ve bağlam `IDbContextFactory`sınıfı ve bir uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="8ef1d-145">Sınıf kitaplığı projesi için PMC'de `Enable-Migrations` `Add-Migration Initial`komutları çalıştırın ve .</span><span class="sxs-lookup"><span data-stu-id="8ef1d-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="8ef1d-146">ASP.NET Core projesini başlangıç projesi olarak ayarladıysanız, bu komutları ekleyin. `-StartupProjectName EF6`</span><span class="sxs-lookup"><span data-stu-id="8ef1d-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="8ef1d-147">Çekirdek projesinde, sınıf kitaplığı projesine bir proje başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="8ef1d-148">Core projesinde, *Startup.cs,* diçin bağlamı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="8ef1d-149">Core projesinde, *appsettings.json,* bağlantı dizesini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="8ef1d-150">Çekirdek projesinde, verileri okuyup yazabileceğinizi doğrulamak için bir denetleyici ve görünüm(ler) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="8ef1d-151">(ASP.NET Core MVC iskelesinin sınıf kitaplığından başvurulan EF6 bağlamıyla çalışmayacağını unutmayın.)</span><span class="sxs-lookup"><span data-stu-id="8ef1d-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="8ef1d-152">Özet</span><span class="sxs-lookup"><span data-stu-id="8ef1d-152">Summary</span></span>

<span data-ttu-id="8ef1d-153">Bu makalede, ASP.NET Core uygulamasında Varlık Çerçevesi 6'yı kullanmak için temel kılavuz sağlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="8ef1d-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ef1d-154">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8ef1d-154">Additional resources</span></span>

* [<span data-ttu-id="8ef1d-155">Varlık Çerçevesi - Kod Tabanlı Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8ef1d-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
