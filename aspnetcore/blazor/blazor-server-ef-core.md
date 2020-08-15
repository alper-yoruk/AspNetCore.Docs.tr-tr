---
title: Blazor ServerEntity Framework Core ile ASP.NET Core (EFCore)
author: JeremyLikness
description: Uygulamalarda EF Core kullanma kılavuzu Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 2ea702aa73a2981afc223e5c1700d6ec2dc62df4
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227798"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="76c49-103">Blazor ServerEntity Framework Core ile ASP.NET Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="76c49-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="76c49-104">Şu kadar: [Jeremy Liksizlik](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="76c49-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="76c49-105">Blazor Server durum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="76c49-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="76c49-106">Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="76c49-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="76c49-107">Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri.</span><span class="sxs-lookup"><span data-stu-id="76c49-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="76c49-108">Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="76c49-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="76c49-109">Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="76c49-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="76c49-110">Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="76c49-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="76c49-111">İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.</span><span class="sxs-lookup"><span data-stu-id="76c49-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="76c49-112">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="76c49-112">Sample app</span></span>

<span data-ttu-id="76c49-113">Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="76c49-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="76c49-114">Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir.</span><span class="sxs-lookup"><span data-stu-id="76c49-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="76c49-115">Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="76c49-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="76c49-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76c49-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="76c49-117">Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="76c49-118">Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="76c49-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="76c49-119">Bu, içinde yapılandırılır `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="76c49-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="76c49-120">Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="76c49-121">Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="76c49-122">Veritabanı erişimi</span><span class="sxs-lookup"><span data-stu-id="76c49-122">Database access</span></span>

<span data-ttu-id="76c49-123">EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [_iş birimi_](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-123">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="76c49-124">EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak _kapsamlı_ bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="76c49-124">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="76c49-125">Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından kapsamlı hizmet kayıtları sorunlu olabilir.</span><span class="sxs-lookup"><span data-stu-id="76c49-125">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="76c49-126"><xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="76c49-126"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="76c49-127">Mevcut yaşam süreleri şu nedenlerle uygun değildir:</span><span class="sxs-lookup"><span data-stu-id="76c49-127">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="76c49-128">**Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.</span><span class="sxs-lookup"><span data-stu-id="76c49-128">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="76c49-129">**Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.</span><span class="sxs-lookup"><span data-stu-id="76c49-129">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="76c49-130">İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.</span><span class="sxs-lookup"><span data-stu-id="76c49-130">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="76c49-131">Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="76c49-131">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="76c49-132">Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="76c49-132">By default, consider using one context per operation.</span></span> <span data-ttu-id="76c49-133">Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="76c49-133">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="76c49-134">Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:</span><span class="sxs-lookup"><span data-stu-id="76c49-134">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* <span data-ttu-id="76c49-135">EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="76c49-135">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="76c49-136">Yeni DbContext örnekleri</span><span class="sxs-lookup"><span data-stu-id="76c49-136">New DbContext instances</span></span>

<span data-ttu-id="76c49-137">Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="76c49-137">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="76c49-138">Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır.</span><span class="sxs-lookup"><span data-stu-id="76c49-138">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="76c49-139">Örneğin, [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="76c49-139">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="76c49-140">Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="76c49-140">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="76c49-141">EF Core 5,0 veya üzeri, yeni bağlamlar oluşturmaya yönelik yerleşik bir fabrika sağlar.</span><span class="sxs-lookup"><span data-stu-id="76c49-141">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="76c49-142">Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="76c49-142">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="76c49-143">Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="76c49-143">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="76c49-144">Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="76c49-144">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="76c49-145">Örneğin, içinde `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="76c49-145">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="76c49-146">Bileşen ömrü için kapsam</span><span class="sxs-lookup"><span data-stu-id="76c49-146">Scope to the component lifetime</span></span>

<span data-ttu-id="76c49-147"><xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="76c49-147">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="76c49-148">Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="76c49-148">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="76c49-149">Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="76c49-149">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="76c49-150">İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="76c49-150">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="76c49-151">Örnek uygulama, bileşen atıldığı zaman kişinin elden çıkarılmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="76c49-151">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="76c49-152">Son olarak, `OnInitializedAsync` Yeni bağlam oluşturmak için geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="76c49-152">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="76c49-153">Örnek uygulamada, `OnInitializedAsync` kişiyi aynı yönteme yükler:</span><span class="sxs-lookup"><span data-stu-id="76c49-153">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="76c49-154">Blazor Server durum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="76c49-154">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="76c49-155">Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="76c49-155">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="76c49-156">Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri.</span><span class="sxs-lookup"><span data-stu-id="76c49-156">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="76c49-157">Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="76c49-157">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="76c49-158">Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="76c49-158">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="76c49-159">Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="76c49-159">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="76c49-160">İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.</span><span class="sxs-lookup"><span data-stu-id="76c49-160">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="76c49-161">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="76c49-161">Sample app</span></span>

<span data-ttu-id="76c49-162">Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="76c49-162">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="76c49-163">Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir.</span><span class="sxs-lookup"><span data-stu-id="76c49-163">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="76c49-164">Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="76c49-164">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="76c49-165">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76c49-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="76c49-166">Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-166">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="76c49-167">Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="76c49-167">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="76c49-168">Bu, içinde yapılandırılır `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="76c49-168">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="76c49-169">Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-169">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="76c49-170">Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-170">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="76c49-171">Veritabanı erişimi</span><span class="sxs-lookup"><span data-stu-id="76c49-171">Database access</span></span>

<span data-ttu-id="76c49-172">EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [_iş birimi_](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="76c49-172">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="76c49-173">EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak _kapsamlı_ bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="76c49-173">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="76c49-174">Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından bu sorun sorunlu olabilir.</span><span class="sxs-lookup"><span data-stu-id="76c49-174">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="76c49-175"><xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="76c49-175"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="76c49-176">Mevcut yaşam süreleri şu nedenlerle uygun değildir:</span><span class="sxs-lookup"><span data-stu-id="76c49-176">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="76c49-177">**Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.</span><span class="sxs-lookup"><span data-stu-id="76c49-177">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="76c49-178">**Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.</span><span class="sxs-lookup"><span data-stu-id="76c49-178">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="76c49-179">İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.</span><span class="sxs-lookup"><span data-stu-id="76c49-179">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="76c49-180">Veritabanı erişimi</span><span class="sxs-lookup"><span data-stu-id="76c49-180">Database access</span></span>

<span data-ttu-id="76c49-181">Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="76c49-181">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="76c49-182">Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="76c49-182">By default, consider using one context per operation.</span></span> <span data-ttu-id="76c49-183">Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="76c49-183">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="76c49-184">Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:</span><span class="sxs-lookup"><span data-stu-id="76c49-184">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* <span data-ttu-id="76c49-185">EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="76c49-185">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="76c49-186">Yeni DbContext örnekleri oluştur</span><span class="sxs-lookup"><span data-stu-id="76c49-186">Create new DbContext instances</span></span>

<span data-ttu-id="76c49-187">Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="76c49-187">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="76c49-188">Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır.</span><span class="sxs-lookup"><span data-stu-id="76c49-188">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="76c49-189">Örneğin, [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="76c49-189">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="76c49-190">Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="76c49-190">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="76c49-191">Örnek uygulama, içinde kendi fabrikasını uygular `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="76c49-191">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span> 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="76c49-192">Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="76c49-192">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="76c49-193">Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="76c49-193">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="76c49-194">Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="76c49-194">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="76c49-195">Örneğin, içinde `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="76c49-195">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="76c49-196">Bileşen ömrü için kapsam</span><span class="sxs-lookup"><span data-stu-id="76c49-196">Scope to the component lifetime</span></span>

<span data-ttu-id="76c49-197"><xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="76c49-197">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="76c49-198">Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="76c49-198">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="76c49-199">Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="76c49-199">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="76c49-200">İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="76c49-200">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="76c49-201">Örnek uygulama, bileşen atıldığı zaman kişinin elden çıkarılmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="76c49-201">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="76c49-202">Son olarak, `OnInitializedAsync` Yeni bağlam oluşturmak için geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="76c49-202">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="76c49-203">Örnek uygulamada, `OnInitializedAsync` kişiyi aynı yönteme yükler:</span><span class="sxs-lookup"><span data-stu-id="76c49-203">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="76c49-204">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="76c49-204">Additional resources</span></span>

> [<span data-ttu-id="76c49-205">EF Core belgeleri</span><span class="sxs-lookup"><span data-stu-id="76c49-205">EF Core documentation</span></span>](/ef/)
