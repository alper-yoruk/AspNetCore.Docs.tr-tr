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
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>Blazor ServerEntity Framework Core ile ASP.NET Core (EFCore)

Şu kadar: [Jeremy Liksizlik](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server durum bilgisi olan bir uygulama çerçevesidir. Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur. Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri. Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir. 

> [!NOTE]
> Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server . Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır. İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.

## <a name="sample-app"></a>Örnek uygulama

Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server . Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir. Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır. Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır. Bu, içinde yapılandırılır `appsettings.Development.json` :

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır. Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.

## <a name="database-access"></a>Veritabanı erişimi

EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [_iş birimi_](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır. EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak _kapsamlı_ bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar. Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından kapsamlı hizmet kayıtları sorunlu olabilir. <xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır. Mevcut yaşam süreleri şu nedenlerle uygun değildir:

* **Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.
* **Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.
* İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.

Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server . 

* Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün. Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:

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

* EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime).

### <a name="new-dbcontext-instances"></a>Yeni DbContext örnekleri

Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır. Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır. Örneğin, [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz. 

Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır. EF Core 5,0 veya üzeri, yeni bağlamlar oluşturmaya yönelik yerleşik bir fabrika sağlar.

Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir. Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır. Örneğin, içinde `Pages/Index.razor` :

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>Bileşen ömrü için kapsam

<xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz. Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.
Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz. İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

Örnek uygulama, bileşen atıldığı zaman kişinin elden çıkarılmasını sağlar:

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

Son olarak, `OnInitializedAsync` Yeni bağlam oluşturmak için geçersiz kılınır. Örnek uygulamada, `OnInitializedAsync` kişiyi aynı yönteme yükler:

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server durum bilgisi olan bir uygulama çerçevesidir. Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur. Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri. Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir. 

> [!NOTE]
> Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server . Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır. İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.

## <a name="sample-app"></a>Örnek uygulama

Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server . Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir. Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır. Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır. Bu, içinde yapılandırılır `appsettings.Development.json` :

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır. Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.

## <a name="database-access"></a>Veritabanı erişimi

EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [_iş birimi_](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır. EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak _kapsamlı_ bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar. Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından bu sorun sorunlu olabilir. <xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır. Mevcut yaşam süreleri şu nedenlerle uygun değildir:

* **Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.
* **Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.
* İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.

## <a name="database-access"></a>Veritabanı erişimi

Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server . 

* Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün. Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:

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

* EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime).

### <a name="create-new-dbcontext-instances"></a>Yeni DbContext örnekleri oluştur

Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır. Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır. Örneğin, [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz. 

Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır. Örnek uygulama, içinde kendi fabrikasını uygular `Data/DbContextFactory.cs` . 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir. Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır. Örneğin, içinde `Pages/Index.razor` :

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>Bileşen ömrü için kapsam

<xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz. Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.
Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz. İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

Örnek uygulama, bileşen atıldığı zaman kişinin elden çıkarılmasını sağlar:

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

Son olarak, `OnInitializedAsync` Yeni bağlam oluşturmak için geçersiz kılınır. Örnek uygulamada, `OnInitializedAsync` kişiyi aynı yönteme yükler:

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

> [EF Core belgeleri](/ef/)
