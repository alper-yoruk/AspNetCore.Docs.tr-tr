---
title: ASP.NET Core ve Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 ve üzeri ASP.NET Core 3,1 ve üzeri sürümlerle çalışmaktadır.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: c65a1ae7cc67ec7e15a2b732184bc05803eb40ce
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202769"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="ec45d-103">ASP.NET Core ve Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="ec45d-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec45d-104">[Entity Framework](/ef/ef6/) 6,3 ve üzeri ASP.NET Core 3,1 ve üzeri sürümlerle çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ec45d-104">[Entity Framework](/ef/ef6/) 6.3 and later works with ASP.NET Core 3.1 and later.</span></span> <span data-ttu-id="ec45d-105">Daha fazla bilgi için [.NET Core desteğiyle Entity Framework 6,3 duyurusu](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/)başlıklı blog gönderisine bakın.</span><span class="sxs-lookup"><span data-stu-id="ec45d-105">For more information, see the blog post [Announcing Entity Framework 6.3 with .NET Core Support](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec45d-106">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ec45d-106">Additional resources</span></span>

* [<span data-ttu-id="ec45d-107">Kod tabanlı Entity Framework yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ec45d-107">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec45d-108">By [Paweł Grudzień](https://github.com/pgrudzien12), [Davmien Pontıex](https://github.com/DamienPontifex)ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="ec45d-108">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="ec45d-109">Bu makalede, Entity Framework 6 ' nın ASP.NET Core bir uygulamada nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-109">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="ec45d-110">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="ec45d-110">Overview</span></span> 

<span data-ttu-id="ec45d-111">Entity Framework 6 ' yı kullanmak için, Entity Framework 6 ' nın .NET Core 'u desteklemediği için, projenizin .NET Framework karşı derlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-111">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="ec45d-112">Platformlar arası özelliklere ihtiyacınız varsa [Entity Framework Core](/ef/)yükseltmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-112">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="ec45d-113">ASP.NET Core uygulamasında 6 Entity Framework kullanmanın önerilen yolu, EF6 bağlamını ve model sınıflarını .NET Framework hedefleyen bir sınıf kitaplığı projesine koykullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="ec45d-113">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="ec45d-114">ASP.NET Core projesinden sınıf kitaplığına bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ec45d-114">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="ec45d-115">Örnek [Visual Studio çözümüne EF6 ve ASP.NET Core projelerine](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)bakın.</span><span class="sxs-lookup"><span data-stu-id="ec45d-115">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="ec45d-116">.NET Core projeleri, *Enable-geçişler* gibi EF6 komutlarının tüm işlevlerini desteklemediğinden, bir ASP.NET Core projesine EF6 bağlamı koyamazsınız.</span><span class="sxs-lookup"><span data-stu-id="ec45d-116">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="ec45d-117">EF6 bağlamını bulmakta olduğunuz proje türünden bağımsız olarak, yalnızca EF6 komut satırı araçları bir EF6 bağlamıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="ec45d-117">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="ec45d-118">Örneğin, `Scaffold-DbContext` yalnızca Entity Framework Core kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-118">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="ec45d-119">Bir veritabanının EF6 modeline ters mühendislik uygulamanız gerekiyorsa bkz. [var olan bir veritabanına Code First](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="ec45d-119">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>  

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="ec45d-120">ASP.NET Core projesindeki tam Framework ve EF6 başvurusu</span><span class="sxs-lookup"><span data-stu-id="ec45d-120">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="ec45d-121">ASP.NET Core projenizin .NET Framework ve başvuru EF6 hedeflemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-121">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="ec45d-122">Örneğin, ASP.NET Core projenizin *. csproj* dosyası aşağıdaki örneğe benzer olacaktır (yalnızca dosyanın ilgili bölümleri gösterilir).</span><span class="sxs-lookup"><span data-stu-id="ec45d-122">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="ec45d-123">Yeni bir proje oluştururken **ASP.NET Core Web uygulaması (.NET Framework)** şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="ec45d-123">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="ec45d-124">Bağlantı dizelerini işle</span><span class="sxs-lookup"><span data-stu-id="ec45d-124">Handle connection strings</span></span>    

<span data-ttu-id="ec45d-125">EF6 sınıf kitaplığı projesinde kullanacağınız EF6 komut satırı araçları, bağlamı örneklenebilen bir varsayılan Oluşturucu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-125">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="ec45d-126">Ancak büyük olasılıkla, ASP.NET Core projesinde kullanılacak bağlantı dizesini belirtmek isteyeceksiniz. Bu durumda, bağlam oluşturucunun bağlantı dizesinde geçiş yapmanızı sağlayan bir parametreye sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-126">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="ec45d-127">Bir örneği aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-127">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="ec45d-128">EF6 içeriğiniz parametresiz bir oluşturucuya sahip olmadığından, EF6 projenizin bir [ıdbcontextfactory](https://msdn.microsoft.com/library/hh506876)uygulamasını sağlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-128">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="ec45d-129">EF6 komut satırı araçları, bağlamı örneklebilmeleri için bu uygulamayı bulup kullanacaktır.</span><span class="sxs-lookup"><span data-stu-id="ec45d-129">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="ec45d-130">Bir örneği aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-130">Here's an example.</span></span>    

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="ec45d-131">Bu örnek kodda, `IDbContextFactory` Uygulama sabit kodlanmış bir bağlantı dizesinde geçirilir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-131">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="ec45d-132">Bu, komut satırı araçlarının kullanacağı bağlantı dizesidir.</span><span class="sxs-lookup"><span data-stu-id="ec45d-132">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="ec45d-133">Sınıf kitaplığının çağıran uygulamanın kullandığı bağlantı dizesini kullandığından emin olmak için bir strateji uygulamak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="ec45d-133">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="ec45d-134">Örneğin, her iki projedeki bir ortam değişkeninden değeri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ec45d-134">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="ec45d-135">ASP.NET Core projesine bağımlılık ekleme işlemini ayarlama</span><span class="sxs-lookup"><span data-stu-id="ec45d-135">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="ec45d-136">Çekirdek projenin *Startup.cs* dosyasında, içindeki bağımlılık ekleme (dı) için EF6 bağlamını ayarlayın `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ec45d-136">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="ec45d-137">EF bağlam nesneleri, istek başına ömür için kapsamı belirlenmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ec45d-137">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="ec45d-138">Daha sonra, DI kullanarak denetleyicilerinizdeki bağlamın bir örneğini alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ec45d-138">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="ec45d-139">Kod, EF Core bağlamı için yazdıklarınız ile benzerdir:</span><span class="sxs-lookup"><span data-stu-id="ec45d-139">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="ec45d-140">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="ec45d-140">Sample application</span></span>   

<span data-ttu-id="ec45d-141">Çalışan bir örnek uygulama için, bu makaleye eşlik eden [örnek Visual Studio çözümü](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="ec45d-141">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="ec45d-142">Bu örnek, Visual Studio 'da aşağıdaki adımlarla sıfırdan oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="ec45d-142">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="ec45d-143">Bir çözüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ec45d-143">Create a solution.</span></span>    

* <span data-ttu-id="ec45d-144">**Ekle** > **Yeni proje** > **Web** > **ASP.NET Core Web uygulaması**</span><span class="sxs-lookup"><span data-stu-id="ec45d-144">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="ec45d-145">Proje şablonu seçimi iletişim kutusunda, açılan menüde API ve .NET Framework seçin</span><span class="sxs-lookup"><span data-stu-id="ec45d-145">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="ec45d-146">**Ekle** > **Yeni proje** > **Windows Masaüstü** > **Sınıf kitaplığı (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="ec45d-146">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="ec45d-147">Her iki proje için de **Paket Yöneticisi konsolunda** (PMC) komutunu çalıştırın `Install-Package Entityframework` .</span><span class="sxs-lookup"><span data-stu-id="ec45d-147">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="ec45d-148">Sınıf kitaplığı projesinde, veri modeli sınıfları ve bağlam sınıfı ve uygulamasını oluşturun `IDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="ec45d-148">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="ec45d-149">Sınıf kitaplığı projesi için PMC 'de, ve komutlarını çalıştırın `Enable-Migrations` `Add-Migration Initial` .</span><span class="sxs-lookup"><span data-stu-id="ec45d-149">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="ec45d-150">ASP.NET Core projesini başlangıç projesi olarak ayarladıysanız, `-StartupProjectName EF6` Bu komutlara ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ec45d-150">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="ec45d-151">Çekirdek projede, sınıf kitaplığı projesine bir proje başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ec45d-151">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="ec45d-152">Çekirdek projede, *Startup.cs*IÇINDE, dı için bağlamını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="ec45d-152">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="ec45d-153">Çekirdek projede, *appsettings.jsüzerinde*, bağlantı dizesini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ec45d-153">In the Core project, in *appsettings.json*, add the connection string.</span></span>    

* <span data-ttu-id="ec45d-154">Temel projede, verileri okuyabildiğinizi ve yazabildiğinizi doğrulamak için bir denetleyici ve görünüm ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ec45d-154">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="ec45d-155">(ASP.NET Core MVC yapı iskelesi, sınıf kitaplığından başvurulan EF6 bağlamıyla çalışmaz.)</span><span class="sxs-lookup"><span data-stu-id="ec45d-155">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
