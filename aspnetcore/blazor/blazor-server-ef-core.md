---
title: Blazor ServerEntity Framework Core ile ASP.NET Core (EFCore)
author: JeremyLikness
description: Uygulamalarda EF Core kullanma kılavuzu Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 36c0915649d9a25cf1b6b2d9ec69c3919aa7bc2c
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424171"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="7d542-103">Blazor ServerEntity Framework Core ile ASP.NET Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="7d542-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="7d542-104">Şu kadar: [Jeremy Liksizlik](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="7d542-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7d542-105">Blazor Server durum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="7d542-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="7d542-106">Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="7d542-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="7d542-107">Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri.</span><span class="sxs-lookup"><span data-stu-id="7d542-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="7d542-108">Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7d542-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="7d542-109">Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7d542-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="7d542-110">Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="7d542-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="7d542-111">İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.</span><span class="sxs-lookup"><span data-stu-id="7d542-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="7d542-112">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="7d542-112">Sample app</span></span></h2>

<span data-ttu-id="7d542-113">Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7d542-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="7d542-114">Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir.</span><span class="sxs-lookup"><span data-stu-id="7d542-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="7d542-115">Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7d542-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="7d542-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7d542-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7d542-117">Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="7d542-118">Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7d542-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="7d542-119">Bu, içinde yapılandırılır `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="7d542-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="7d542-120">Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="7d542-121">Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="7d542-122">Bu konudaki bazı kod örnekleri, gösterilmeyen ad alanlarını ve Hizmetleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7d542-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="7d542-123">Gerekli ve örnek yönergeleri dahil olmak üzere tam çalışma kodunu incelemek için [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="7d542-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="7d542-124">Veritabanı erişimi</span><span class="sxs-lookup"><span data-stu-id="7d542-124">Database access</span></span></h2>

<span data-ttu-id="7d542-125">EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [*iş birimi*](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="7d542-126">EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak *kapsamlı* bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d542-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="7d542-127">Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından kapsamlı hizmet kayıtları sorunlu olabilir.</span><span class="sxs-lookup"><span data-stu-id="7d542-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="7d542-128"><xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="7d542-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="7d542-129">Mevcut yaşam süreleri şu nedenlerle uygun değildir:</span><span class="sxs-lookup"><span data-stu-id="7d542-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="7d542-130">**Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d542-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="7d542-131">**Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7d542-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="7d542-132">İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.</span><span class="sxs-lookup"><span data-stu-id="7d542-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="7d542-133">Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7d542-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="7d542-134">Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="7d542-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="7d542-135">Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="7d542-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="7d542-136">Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:</span><span class="sxs-lookup"><span data-stu-id="7d542-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="7d542-137">`Loading = true;`Blok içindeki satırdan sonra işlemleri yerleştir `try` .</span><span class="sxs-lookup"><span data-stu-id="7d542-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="7d542-138">EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="7d542-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="7d542-139">Yeni DbContext örnekleri</span><span class="sxs-lookup"><span data-stu-id="7d542-139">New DbContext instances</span></span></h3>

<span data-ttu-id="7d542-140">Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7d542-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="7d542-141">Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır.</span><span class="sxs-lookup"><span data-stu-id="7d542-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="7d542-142">Örneğin, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7d542-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="7d542-143">Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7d542-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="7d542-144">EF Core 5,0 veya üzeri, yeni bağlamlar oluşturmaya yönelik yerleşik bir fabrika sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d542-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="7d542-145">Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7d542-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="7d542-146">Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="7d542-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="7d542-147">Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7d542-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="7d542-148">Örneğin, içinde `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="7d542-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="7d542-149">`Wrapper` bileşene bir [bileşen başvurusu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` .</span><span class="sxs-lookup"><span data-stu-id="7d542-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="7d542-150">`Index` `Pages/Index.razor` [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)bileşene () bakın.</span><span class="sxs-lookup"><span data-stu-id="7d542-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="7d542-151">Bileşen ömrü için kapsam</span><span class="sxs-lookup"><span data-stu-id="7d542-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="7d542-152"><xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7d542-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="7d542-153">Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d542-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="7d542-154">Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7d542-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="7d542-155">İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="7d542-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="7d542-156">Örnek uygulama, bileşen bırakıldığında bağlamın elden çıkarılmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="7d542-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="7d542-157">Son olarak, [`OnInitializedAsync`](xref:blazor/components/lifecycle) Yeni bağlam oluşturmak için geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="7d542-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="7d542-158">Örnek uygulamada, [`OnInitializedAsync`](xref:blazor/components/lifecycle) kişiyi aynı yönteme yükler:</span><span class="sxs-lookup"><span data-stu-id="7d542-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="7d542-159">Blazor Server durum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="7d542-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="7d542-160">Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="7d542-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="7d542-161">Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri.</span><span class="sxs-lookup"><span data-stu-id="7d542-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="7d542-162">Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7d542-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="7d542-163">Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7d542-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="7d542-164">Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="7d542-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="7d542-165">İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.</span><span class="sxs-lookup"><span data-stu-id="7d542-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="7d542-166">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="7d542-166">Sample app</span></span></h2>

<span data-ttu-id="7d542-167">Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7d542-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="7d542-168">Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir.</span><span class="sxs-lookup"><span data-stu-id="7d542-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="7d542-169">Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7d542-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="7d542-170">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7d542-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7d542-171">Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="7d542-172">Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7d542-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="7d542-173">Bu, içinde yapılandırılır `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="7d542-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="7d542-174">Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="7d542-175">Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="7d542-176">Bu konudaki bazı kod örnekleri, gösterilmeyen ad alanlarını ve Hizmetleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7d542-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="7d542-177">Gerekli ve örnek yönergeleri dahil olmak üzere tam çalışma kodunu incelemek için [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="7d542-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="7d542-178">Veritabanı erişimi</span><span class="sxs-lookup"><span data-stu-id="7d542-178">Database access</span></span></h2>

<span data-ttu-id="7d542-179">EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [*iş birimi*](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="7d542-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="7d542-180">EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak *kapsamlı* bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d542-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="7d542-181">Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından bu sorun sorunlu olabilir.</span><span class="sxs-lookup"><span data-stu-id="7d542-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="7d542-182"><xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="7d542-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="7d542-183">Mevcut yaşam süreleri şu nedenlerle uygun değildir:</span><span class="sxs-lookup"><span data-stu-id="7d542-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="7d542-184">**Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d542-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="7d542-185">**Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7d542-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="7d542-186">İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.</span><span class="sxs-lookup"><span data-stu-id="7d542-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="7d542-187">Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="7d542-187">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="7d542-188">Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="7d542-188">By default, consider using one context per operation.</span></span> <span data-ttu-id="7d542-189">Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="7d542-189">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="7d542-190">Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:</span><span class="sxs-lookup"><span data-stu-id="7d542-190">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="7d542-191">`Loading = true;`Blok içindeki satırdan sonra işlemleri yerleştir `try` .</span><span class="sxs-lookup"><span data-stu-id="7d542-191">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="7d542-192">EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="7d542-192">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="7d542-193">Yeni DbContext örnekleri</span><span class="sxs-lookup"><span data-stu-id="7d542-193">New DbContext instances</span></span></h3>

<span data-ttu-id="7d542-194">Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7d542-194">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="7d542-195">Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır.</span><span class="sxs-lookup"><span data-stu-id="7d542-195">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="7d542-196">Örneğin, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7d542-196">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="7d542-197">Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7d542-197">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="7d542-198">Örnek uygulama, içinde kendi fabrikasını uygular `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="7d542-198">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="7d542-199">Önceki fabrikada:</span><span class="sxs-lookup"><span data-stu-id="7d542-199">In the preceding factory:</span></span>

* <span data-ttu-id="7d542-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> hizmet sağlayıcı aracılığıyla herhangi bir bağımlılığı karşılar.</span><span class="sxs-lookup"><span data-stu-id="7d542-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="7d542-201">`IDbContextFactory` EF Core ASP.NET Core 5,0 veya sonraki sürümlerde kullanılabilir. bu nedenle arabirim, [ASP.NET Core 3. x için örnek uygulamada uygulanır](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="7d542-201">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="7d542-202">Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7d542-202">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="7d542-203">Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:</span><span class="sxs-lookup"><span data-stu-id="7d542-203">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="7d542-204">Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7d542-204">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="7d542-205">Örneğin, içinde `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="7d542-205">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="7d542-206">`Wrapper` bileşene bir [bileşen başvurusu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` .</span><span class="sxs-lookup"><span data-stu-id="7d542-206">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="7d542-207">`Index` `Pages/Index.razor` [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)bileşene () bakın.</span><span class="sxs-lookup"><span data-stu-id="7d542-207">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="7d542-208">Bileşen ömrü için kapsam</span><span class="sxs-lookup"><span data-stu-id="7d542-208">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="7d542-209"><xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7d542-209">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="7d542-210">Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d542-210">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="7d542-211">Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7d542-211">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="7d542-212">İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="7d542-212">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="7d542-213">Örnek uygulama, bileşen bırakıldığında bağlamın elden çıkarılmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="7d542-213">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="7d542-214">Son olarak, [`OnInitializedAsync`](xref:blazor/components/lifecycle) Yeni bağlam oluşturmak için geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="7d542-214">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="7d542-215">Örnek uygulamada, [`OnInitializedAsync`](xref:blazor/components/lifecycle) kişiyi aynı yönteme yükler:</span><span class="sxs-lookup"><span data-stu-id="7d542-215">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="7d542-216">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="7d542-216">In the preceding example:</span></span>

* <span data-ttu-id="7d542-217">, `Busy` Olarak ayarlandığında `true` , zaman uyumsuz işlemler başlayabilir.</span><span class="sxs-lookup"><span data-stu-id="7d542-217">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="7d542-218">, `Busy` ' A geri ayarlandığında `false` , zaman uyumsuz işlemlerin bitmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7d542-218">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="7d542-219">Bir blokta ek hata işleme mantığı yerleştirin `catch` .</span><span class="sxs-lookup"><span data-stu-id="7d542-219">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7d542-220">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7d542-220">Additional resources</span></span>

* [<span data-ttu-id="7d542-221">EF Core belgeleri</span><span class="sxs-lookup"><span data-stu-id="7d542-221">EF Core documentation</span></span>](/ef/)
