---
title: ASP.NET Core'da bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core'un bağımlılık enjeksiyonunu nasıl uyguladığını ve nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 943ea30c2e4887638f69b6dcdb7e323bcee40240
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405975"
---
# <a name="dependency-injection-in-aspnet-core"></a>ASP.NET Core'da bağımlılık ekleme

Yazar: [Steve Smith](https://ardalis.com/) ve [Scott Addie](https://scottaddie.com)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [Kontrol Inversion (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik tir bağımlılık enjeksiyon (DI) yazılım tasarım deseni destekler.

MVC denetleyicileri içinde bağımlılık enjeksiyonuna özgü <xref:mvc/controllers/dependency-injection>daha fazla bilgi için bkz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Bağımlılık enjeksiyonuna genel bakış

*Bağımlılık,* başka bir nesnenin gerektirdiği herhangi bir nesnedir. Bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle aşağıdaki `MyDependency` sınıfı inceleyin:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Yöntemi bir `MyDependency` sınıf için kullanılabilir hale getirmek için sınıfın bir örneği oluşturulabilir. `WriteMessage` Sınıf `MyDependency` sınıfın bir bağımlılıktır: `IndexModel`

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

Sınıf oluşturur ve doğrudan örneğin `MyDependency` bağlıdır. Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunludur ve aşağıdaki nedenlerden dolayı kaçınılmalıdır:

* Farklı `MyDependency` bir uygulamayla değiştirmek için sınıfın değiştirilmesi gerekir.
* Bağımlılıkları `MyDependency` varsa, sınıf tarafından yapılandırılmalıdır. Bağlı olarak birden fazla sınıfa `MyDependency`sahip büyük bir projede, yapılandırma kodu uygulamaya dağılır.
* Bu uygulamayı birleştirmek zordur. Uygulama, bu yaklaşımla mümkün `MyDependency` olmayan bir sahte veya saplama sınıfı kullanmalıdır.

Bağımlılık enjeksiyonu bu sorunları şu yollarla giderir:

* Bağımlılık uygulamasını soyutlamak için arabirim veya taban sınıf kullanımı.
* Bir hizmet kapsayıcısında ki bağımlılığın kaydı. ASP.NET Core dahili servis konteyneri sağlar. <xref:System.IServiceProvider> Hizmetler uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.
* Hizmetin kullanıldığı sınıfın yapıcısına *enjeksiyonu.* Çerçeve, artık ihtiyaç duyulmadığında bağımlılığın bir örneğini oluşturma ve ortadan çıkarma sorumluluğunu üstlenir.

Örnek [uygulamada,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Bu arayüz, somut bir tür `MyDependency`tarafından uygulanır:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`onun <xref:Microsoft.Extensions.Logging.ILogger`1> yapıcı bir talep. Bağımlılık enjeksiyonunu zincirli bir şekilde kullanmak alışılmadık bir şey değil. İstenen her bağımlılık sırayla kendi bağımlılıklarını ister. Kapsayıcı grafikteki bağımlılıkları giderir ve tam olarak çözülmüş hizmeti döndürür. Çözülmesi gereken toplu bağımlılıkkümesi genellikle *bağımlılık ağacı,* *bağımlılık grafiği*veya nesne *grafiği*olarak adlandırılır.

`IMyDependency`ve `ILogger<TCategoryName>` servis konteynerine kayıtlı olmalıdır. `IMyDependency``Startup.ConfigureServices`kayıtlıdır. `ILogger<TCategoryName>`günlük soyutlamaaltyapısı tarafından kaydedilir, bu nedenle çerçeve tarafından varsayılan olarak kaydedilmiş bir [çerçeve tarafından sağlanan](#framework-provided-services) bir hizmettir.

Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türleri](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak giderir , her [(genel) inşa türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kayıt ihtiyacını ortadan kaldırarak:

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Örnek uygulamada, `IMyDependency` hizmet beton türüne `MyDependency`kaydedilir. Kayıt, hizmet ömrünü tek bir isteğin ömrüne kadar kapsamlıdır. [Hizmet ömürleri](#service-lifetimes) bu konuda daha sonra açıklanmıştır.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Her `services.Add{SERVICE_NAME}` uzantı yöntemi hizmetleri ekler (ve büyük ölçüde yapılandırır). Örneğin, `services.AddMvc()` Razor Pages ve MVC'nin gerektirdiği hizmetleri ekler. Uygulamaların bu kuralı izlemelerini tavsiye ettik. Hizmet kayıtları gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.

Hizmetin oluşturucusu, yapılandırma [veya](xref:fundamentals/configuration/index) [seçenekler deseni](xref:fundamentals/configuration/options)kullanılarak enjekte edilebilir, bir , bir `string`gibi yerleşik bir [tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektirir:

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın oluşturucusu aracılığıyla istenir. Alan, sınıf boyunca gerektiğinde hizmete erişmek için kullanılır.

Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini aramak için kullanılır:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Başlangıç'a enjekte edilen hizmetler

Genel Ana Bilgisayar kullanırken `Startup` oluşturucuya yalnızca aşağıdaki hizmet<xref:Microsoft.Extensions.Hosting.IHostBuilder>türleri enjekte edilebilir ( ):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Hizmetler enjekte `Startup.Configure`edilebilir:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Çerçeve tarafından sağlanan hizmetler

Yöntem, `Startup.ConfigureServices` Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur. Başlangıçta, `IServiceCollection` sağlanan `ConfigureServices` hizmetler [ana bilgisayar yapılandırılmıştır nasıl](xref:fundamentals/index#host)bağlı olarak çerçeve tarafından tanımlanan vardır. ASP.NET Core şablonuna dayalı bir uygulamanın çerçeve tarafından yüzlerce hizmetin kaydolması nadir değildir. Çerçeveye kayıtlı hizmetlerin küçük bir örneği aşağıdaki tabloda listelenmiştir.

| Hizmet Türü | Ömür |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Geçi -ci |
| `IHostApplicationLifetime` | Singleton |
| `IWebHostEnvironment` | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Geçi -ci |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Geçi -ci |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Geçi -ci |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Ek hizmetleri genişletme yöntemleriyle kaydetme

Bir hizmeti kaydetmek için bir hizmet toplama uzantısı yöntemi (ve gerekirse bağımlı hizmetleri) `Add{SERVICE_NAME}` kullanılabilir olduğunda, sözleşme, söz le ilgili hizmetin gerektirdiği tüm hizmetleri kaydetmek için tek bir uzantı yöntemi kullanmaktır. Aşağıdaki kod, [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ve aşağıdaki uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetlerin nasıl ekleyeceğinize bir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>örnektir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Daha fazla bilgi <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> için API belgelerindeki sınıfa bakın.

## <a name="service-lifetimes"></a>Hizmet ömürleri

Her kayıtlı hizmet için uygun bir ömür seçin. ASP.NET Core hizmetleri aşağıdaki yaşam süreleriyle yapılandırılabilir:

### <a name="transient"></a>Geçi -ci

Geçici yaşam süresi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>hizmetleri ( ) hizmet kapsayıcısından her istendiğinde oluşturulur. Bu ömür, hafif ve devletsiz hizmetler için en iyi şekilde çalışır.

### <a name="scoped"></a>Kapsamlı

Kapsamlı yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>süresi hizmetleri ( ) istemci isteği (bağlantı) başına bir kez oluşturulur.

> [!WARNING]
> Bir ara yazılımda kapsamlı bir hizmet kullanırken, `Invoke` `InvokeAsync` hizmeti veya yöntemini enjekte edin. Yapıcı enjeksiyon yoluyla enjekte etmeyin çünkü hizmeti tek ton gibi olmaya zorlar. Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Singleton yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>boyu hizmetleri ( ) ilk kez istendiğinde `Startup.ConfigureServices` oluşturulur (veya çalıştırıldığında ve hizmet kaydıyla bir örnek belirtilir). Sonraki her istek aynı örneği kullanır. Uygulama singleton davranışı gerektiriyorsa, hizmet kapsayıcısının hizmetin kullanım ömrünü yönetmesine izin vermek önerilir. Singleton tasarım deseni uygulamayın ve nesnenin sınıftaki ömrünü yönetmek için kullanıcı kodu sağlamayın.

> [!WARNING]
> Tek tonlu bir hizmeti çözmek tehlikelidir. Sonraki istekleri işlerken hizmetin yanlış duruma sahip olmasına neden olabilir.

## <a name="service-registration-methods"></a>Hizmet kayıt yöntemleri

Hizmet kaydı uzatma yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.

| Yöntem | Automatic<br>object<br>Elden çıkarma | Birden çok<br>uygulamalar | Pass args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<IMyDep, MyDep>();` | Evet | Evet | Hayır |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Evet | Evet | Evet |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<MyDep>();` | Evet | Hayır | Hayır |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Hayır | Evet | Evet |
| `AddSingleton(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Hayır | Hayır | Evet |

Tür elden çıkarma hakkında daha fazla bilgi [için, hizmetlerin Elden Çıkarılması](#disposal-of-services) bölümüne bakın. Birden çok uygulama için ortak bir [senaryo, sınama türleri ile alay ediyor.](xref:test/integration-tests#inject-mock-services)

`TryAdd{LIFETIME}`yöntemler yalnızca kayıtlı bir uygulama yoksa hizmeti kaydedin.

Aşağıdaki örnekte, ilk satır `MyDependency` için `IMyDependency`kaydeder. `IMyDependency` Zaten kayıtlı bir uygulama olduğundan ikinci satırın hiçbir etkisi yoktur:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Daha fazla bilgi için bkz.

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder. Birden çok hizmet `IEnumerable<{SERVICE}>`. Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri henüz eklenmemişse bir örnek eklemek ister. Genellikle, bu yöntem kapsayıcıda bir örneğin iki kopyasını kaydetmeyi önlemek için kitaplık yazarları tarafından kullanılır.

Aşağıdaki örnekte, ilk satır `MyDep` için `IMyDep1`kaydeder. İkinci satır `MyDep` için `IMyDep2`kaydeder. Zaten kayıtlı bir uygulama `IMyDep1` vardır, çünkü `MyDep`üçüncü satırın hiçbir etkisi yoktur:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Yapıcı enjeksiyon davranışı

Hizmetler iki mekanizma yla çözülebilir:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık enjeksiyon konteynerinde hizmet kaydı olmadan nesne oluşturmaya izin verir. `ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model bağlayıcıları gibi kullanıcıya yönelik soyutlamalarla kullanılır.

Oluşturucular bağımlılık enjeksiyonu tarafından sağlanmadı bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenler varsayılan değerleri atamalıdır.

Hizmetler tarafından `IServiceProvider` çözüldüğünde `ActivatorUtilities`veya , yapıcı enjeksiyon bir *kamu* yapıcı gerektirir.

Hizmetler tarafından `ActivatorUtilities`çözüldüğünde, yapıcı enjeksiyon sadece bir uygulanabilir yapıcı var gerektirir. Yapıcı aşırı yükler desteklenir, ancak bağımsız değişkenleri bağımlılık enjeksiyonu yla yerine getirilebilen yalnızca bir aşırı yükleme olabilir.

## <a name="entity-framework-contexts"></a>Varlık Çerçevesi bağlamları

Varlık Çerçevesi bağlamları genellikle [kapsamlı yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir, çünkü web uygulaması veritabanı işlemleri normalde istemci isteğine yöneliktir. Veritabanı bağlamını kaydederken bir [adddbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklenme tarafından bir ömür belirtilmemişse varsayılan yaşam süresi kapsamlıdır. Belirli bir yaşam süresinin hizmetleri, hizmetten daha kısa bir kullanım ömrüne sahip bir veritabanı bağlamı kullanmamalıdır.

## <a name="lifetime-and-registration-options"></a>Ömür boyu ve kayıt seçenekleri

Kullanım ömrü ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir `OperationId`işlem olarak temsil eden aşağıdaki arabirimleri düşünün. Bir işlem hizmetinin kullanım ömrünün aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak, kapsayıcı bir sınıf tarafından istendiğinde hizmetin aynı veya farklı bir örneğini sağlar:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Arabirimler `Operation` sınıfta uygulanır. Oluşturucu, `Operation` sağlanmazsa bir GUID oluşturur:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Diğer `OperationService` `Operation` türlerin her birine bağlı olarak bir kayıtlıdır. Bağımlılık `OperationService` enjeksiyonu yoluyla istendiğinde, bağımlı hizmetin kullanım ömrüne bağlı olarak her hizmetin yeni bir örneğini veya varolan bir örneği alır.

* Kapsayıcıdan istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmetin .'den `OperationId` farkı `OperationService`vardır. `OperationService`sınıfın yeni bir `IOperationTransient` örneğini alır. Yeni örnek farklı `OperationId`bir verim verir.
* Kapsamlı hizmetler istemci isteği başına `OperationId` oluşturulduğunda, `IOperationScoped` hizmetin istemci `OperationService` isteği içindekiyle aynıdır. İstemci istekleri arasında, `OperationId` her iki hizmet de farklı bir değeri paylaşır.
* Singleton ve singleton-instance hizmetleri bir kez oluşturulduğunda ve tüm `OperationId` istemci istekleri ve tüm hizmetler arasında kullanıldığında, tüm hizmet istekleri arasında sabittir.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

Her `Startup.ConfigureServices`tür, kapta adlandırılmış ömrüne göre eklenir:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Hizmet, `IOperationSingletonInstance` bilinen bir kimliği olan belirli `Guid.Empty`bir örneği kullanıyor. Bu tür kullanımda olduğunda açıktır (ONUN GUID tüm sıfırlar).

Örnek uygulama, tek tek istekler içinde ve arasında nesne yaşam ömürlerini gösterir. Örnek uygulamanın `IndexModel` her `IOperation` tür ve `OperationService`. Sayfa daha sonra özellik atamaları aracılığıyla sayfa modeli `OperationId` sınıfının ve hizmet değerlerinin tümlerini görüntüler:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Aşağıdaki iki çıktı iki isteğin sonuçlarını gösterir:

**İlk istek:**

Denetleyici işlemleri:

Geçici: d233e165-f417-469b-a866-1cf1935d2518  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

`OperationService`Işlem:

Geçici: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

**İkinci istek:**

Denetleyici işlemleri:

Geçici: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

`OperationService`Işlem:

Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

Değerlerden hangisinin `OperationId` istek içinde ve istekler arasında farklılık gösterdiğini gözlemleyin:

* *Geçici* nesneler her zaman farklıdır. Hem birinci `OperationId` hem de ikinci istemci istekleri için `OperationService` geçici değer hem işlemler hem de istemci istekleri arasında farklıdır. Her hizmet isteği ve istemci isteği için yeni bir örnek sağlanır.
* *Kapsamlı* nesneler istemci isteği içinde aynıdır, ancak istemci istekleri arasında farklıdır.
* *Singleton* nesneleri, bir `Operation` örneğin sağlanıp sağlanmadığına bakılmaksızın her `Startup.ConfigureServices`nesne ve her istek için aynıdır.

## <a name="call-services-from-main"></a>Ana hizmetten çağrı hizmetleri

Uygulama <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> kapsamındaki kapsamlı bir hizmeti çözmek için [iServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir uygulama oluşturun. Bu yaklaşım, başlatma görevlerini çalıştırmak için başlangıçta kapsamlı bir hizmete erişmek için yararlıdır. Aşağıdaki örnek, aşağıdaki `MyScopedService` `Program.Main`için bir bağlam nasıl elde edilebildiğini gösterir:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a>Kapsam doğrulama

Uygulama Geliştirme ortamında çalışırken ve ana bilgisayarı oluşturmak için [CreateDefaultBuilder'ı](xref:fundamentals/host/generic-host#default-builder-settings) aradığında, varsayılan hizmet sağlayıcısı bunu doğrulamak için denetimler gerçekleştirir:

* Kapsamlı hizmetler, kök hizmet sağlayıcısından doğrudan veya dolaylı olarak çözülmez.
* Kapsamlı hizmetler doğrudan veya dolaylı olarak singleton içine enjekte değildir.

Kök hizmet sağlayıcısı çağrıldığında <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> oluşturulur. Kök servis sağlayıcısının ömrü, sağlayıcı nın uygulamayla birlikte işe başlaması ve uygulama kapandığında imha edilmesiyle uygulamanın/sunucunun ömrüne karşılık gelir.

Kapsamlı hizmetler, bunları oluşturan kapsayıcı tarafından bertaraf edilir. Kök kapsayıcıda kapsamlı bir hizmet oluşturulursa, yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından bertaraf edildiğinden, hizmetin ömrü etkin bir şekilde singleton'a yükseltilir. Hizmet kapsamlarını doğrulama çağrıldığında `BuildServiceProvider` bu durumları yakalar.

Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Talep Hizmetleri

ASP.NET Core isteği içinde sunulan `HttpContext` [hizmetler, HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla açıklanır.

İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil ediyor. Nesneler bağımlılıkları belirttiğinde, bunlar , `RequestServices` `ApplicationServices`değil' de bulunan türlerle karşılanır.

Genellikle, uygulama bu özellikleri doğrudan kullanmamalıdır. Bunun yerine, sınıf oluşturucular aracılığıyla sınıfların gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları enjekte etmesine izin verin. Bu, sınaması daha kolay olan sınıflar verir.

> [!NOTE]
> Koleksiyona erişmek için kurucu parametreleri olarak `RequestServices` bağımlılıkları talep etmeyi tercih edin.

## <a name="design-services-for-dependency-injection"></a>Bağımlılık enjeksiyonu için tasarım hizmetleri

En iyi uygulamalar şunlardır:

* Bağımlılıklarını elde etmek için bağımlılık enjeksiyonu kullanmak için tasarım hizmetleri.
* Durum lu, statik sınıflardan ve üyelerden kaçının. Bunun yerine, küresel durum oluşturmaktan kaçınan singleton hizmetlerini kullanmak için uygulamalar tasarlayın.
* Hizmetler içindeki bağımlı sınıfların doğrudan anında anlanmasından kaçının. Kodu belirli bir uygulamaya yönlendirin anlık çiftler.
* Uygulama sınıflarını küçük, iyi faktörlü ve kolayca test edilebilen hale getirin.

Bir sınıfın çok fazla enjekte edilmiş bağımlılığı varsa, bu genellikle sınıfın çok fazla sorumluluğu olduğunun ve [Tek Sorumluluk İlkesi'ni (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal ettiğini gösteren bir işarettir. Bazı sorumluluklarını yeni bir sınıfa taşıyarak sınıfı yeniden düzenlemeyi dene. Razor Pages sayfa modeli sınıflarının ve MVC denetleyici sınıflarının UI endişelerine odaklanması gerektiğini unutmayın. İş kuralları ve veri erişimi uygulama ayrıntıları bu [ayrı endişelere](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.

### <a name="disposal-of-services"></a>Hizmetlerin bertaraf edilmesi

Kapsayıcı, <xref:System.IDisposable.Dispose*> oluşturduğu <xref:System.IDisposable> türleri çağırır. Kullanıcı koduyla kapsayıcıya bir örnek eklenirse, otomatik olarak atılmaz.

Aşağıdaki örnekte, hizmetler servis kapsayıcısı tarafından oluşturulur ve otomatik olarak atılır:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

Aşağıdaki örnekte:

* Hizmet örnekleri servis kapsayıcısı tarafından oluşturulmaz.
* Amaçlanan hizmet ömürleri çerçeve tarafından bilinmemektedir.
* Çerçeve hizmetleri otomatik olarak elden çıkarmaz.
* Hizmetler geliştirici kodunda açıkça belirtinmezse, uygulama kapanana kadar devam eder.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

Hizmet imha seçeneklerinin tartışılması [için, IDisposables'i ASP.NET Core'da elden çıkarmanın dört yolunu](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)görün.

## <a name="default-service-container-replacement"></a>Varsayılan hizmet kapsayıcısı değiştirme

Dahili servis konteyneri, çerçevenin ve çoğu tüketici uygulamasının ihtiyaçlarına hizmet etmek üzere tasarlanmıştır. Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız olmadığı sürece yerleşik kapsayıcıyı kullanmanızı öneririz:

* Özellik enjeksiyonu
* İsme göre enjeksiyon
* Çocuk kapları
* Özel yaşam boyu yönetimi
* `Func<T>`tembel başlatma desteği
* Sözleşme tabanlı kayıt

Aşağıdaki 3 ASP.NET.

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Dryioc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Zarafet](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Zula kutusu](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>İş parçacığı güvenliği

İş parçacığı güvenli singleton hizmetleri oluşturun. Bir singleton hizmetigeçici bir hizmete bağımlıysa, geçici hizmet, singleton tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.

[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)ikinci bağımsız değişkeni gibi tek hizmet fabrika yöntemi, iş parçacığı güvenli olması gerekmez. Bir tür`static`gibi ( ) oluşturucu, tek bir iş parçacığı tarafından bir kez çağrılması garanti.

## <a name="recommendations"></a>Öneriler

* `async/await`ve `Task` tabanlı hizmet çözünürlüğü desteklenmez. C# asynchronous yapıcıları desteklemez; bu nedenle, önerilen desen eşitolarak hizmeti çözdükten sonra eşzamanlı yöntemler kullanmaktır.

* Verileri ve yapılandırmayı doğrudan servis kapsayıcısında depolamaktan kaçının. Örneğin, bir kullanıcının alışveriş sepeti genellikle servis kapsayıcısına eklenmemelidir. Yapılandırma seçenekleri [deseni](xref:fundamentals/configuration/options)kullanmalıdır. Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için var olan "veri tutucu" nesnelerden kaçının. Bu DI üzerinden gerçek öğeyi istemek daha iyidir.

* Hizmetlere statik erişimden kaçının (örneğin, başka bir yerde kullanmak için statik olarak [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) yazarak).

* *Servis bulucu deseni*kullanmaktan kaçının. Örneğin, di yerine <xref:System.IServiceProvider.GetService*> kullanabileceğiniz bir hizmet örneği almak için çağrıda almayın:

  **Yanlış:**

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  **Doğru**:

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* Önlemek için başka bir hizmet bulucu varyasyon çalışma zamanında bağımlılıkları çözen bir fabrika enjekte etmektir. Bu uygulamaların her ikisi de [Kontrol stratejilerinin Ters Çevrilmesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) karıştırır.

* Statik erişimden `HttpContext` kaçının (örneğin, [IHttpContextAccessor.HttpContext).](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)

Tüm öneri kümeleri gibi, bir öneriyi yoksaymanın gerekli olduğu durumlarla karşılaşabilirsiniz. İstisnalar, çerçevenin kendisi içinde nadir görülen&mdash;özel durumlardır.

DI statik /küresel nesne erişim desenleri için bir *alternatiftir.* Statik nesne erişimiyle karıştırırsanız DI'nin faydalarını fark edemeyebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Bağımlılık Enjeksiyonu (MSDN) ile ASP.NET Çekirdekte Temiz Kod Yazma](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Açık Bağımlılıklar İlkesi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Kontrol Kapları ve Bağımlılık Enjeksiyon Deseni (Martin Fowler) Ters](https://www.martinfowler.com/articles/injection.html)
* [Core DI'de birden çok arabirimi olan bir hizmet ASP.NET](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [Kontrol Inversion (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik tir bağımlılık enjeksiyon (DI) yazılım tasarım deseni destekler.

MVC denetleyicileri içinde bağımlılık enjeksiyonuna özgü <xref:mvc/controllers/dependency-injection>daha fazla bilgi için bkz.

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Bağımlılık enjeksiyonuna genel bakış

*Bağımlılık,* başka bir nesnenin gerektirdiği herhangi bir nesnedir. Bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle aşağıdaki `MyDependency` sınıfı inceleyin:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Yöntemi bir `MyDependency` sınıf için kullanılabilir hale getirmek için sınıfın bir örneği oluşturulabilir. `WriteMessage` Sınıf `MyDependency` sınıfın bir bağımlılıktır: `IndexModel`

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

Sınıf oluşturur ve doğrudan örneğin `MyDependency` bağlıdır. Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunludur ve aşağıdaki nedenlerden dolayı kaçınılmalıdır:

* Farklı `MyDependency` bir uygulamayla değiştirmek için sınıfın değiştirilmesi gerekir.
* Bağımlılıkları `MyDependency` varsa, sınıf tarafından yapılandırılmalıdır. Bağlı olarak birden fazla sınıfa `MyDependency`sahip büyük bir projede, yapılandırma kodu uygulamaya dağılır.
* Bu uygulamayı birleştirmek zordur. Uygulama, bu yaklaşımla mümkün `MyDependency` olmayan bir sahte veya saplama sınıfı kullanmalıdır.

Bağımlılık enjeksiyonu bu sorunları şu yollarla giderir:

* Bağımlılık uygulamasını soyutlamak için arabirim veya taban sınıf kullanımı.
* Bir hizmet kapsayıcısında ki bağımlılığın kaydı. ASP.NET Core dahili servis konteyneri sağlar. <xref:System.IServiceProvider> Hizmetler uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.
* Hizmetin kullanıldığı sınıfın yapıcısına *enjeksiyonu.* Çerçeve, artık ihtiyaç duyulmadığında bağımlılığın bir örneğini oluşturma ve ortadan çıkarma sorumluluğunu üstlenir.

Örnek [uygulamada,](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Bu arayüz, somut bir tür `MyDependency`tarafından uygulanır:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`onun <xref:Microsoft.Extensions.Logging.ILogger`1> yapıcı bir talep. Bağımlılık enjeksiyonunu zincirli bir şekilde kullanmak alışılmadık bir şey değil. İstenen her bağımlılık sırayla kendi bağımlılıklarını ister. Kapsayıcı grafikteki bağımlılıkları giderir ve tam olarak çözülmüş hizmeti döndürür. Çözülmesi gereken toplu bağımlılıkkümesi genellikle *bağımlılık ağacı,* *bağımlılık grafiği*veya nesne *grafiği*olarak adlandırılır.

`IMyDependency`ve `ILogger<TCategoryName>` servis konteynerine kayıtlı olmalıdır. `IMyDependency``Startup.ConfigureServices`kayıtlıdır. `ILogger<TCategoryName>`günlük soyutlamaaltyapısı tarafından kaydedilir, bu nedenle çerçeve tarafından varsayılan olarak kaydedilmiş bir [çerçeve tarafından sağlanan](#framework-provided-services) bir hizmettir.

Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türleri](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak giderir , her [(genel) inşa türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kayıt ihtiyacını ortadan kaldırarak:

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Örnek uygulamada, `IMyDependency` hizmet beton türüne `MyDependency`kaydedilir. Kayıt, hizmet ömrünü tek bir isteğin ömrüne kadar kapsamlıdır. [Hizmet ömürleri](#service-lifetimes) bu konuda daha sonra açıklanmıştır.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Her `services.Add{SERVICE_NAME}` uzantı yöntemi hizmetleri ekler (ve büyük ölçüde yapılandırır). Örneğin, `services.AddMvc()` Razor Pages ve MVC'nin gerektirdiği hizmetleri ekler. Uygulamaların bu kuralı izlemelerini tavsiye ettik. Hizmet kayıtları gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.

Hizmetin oluşturucusu, yapılandırma [veya](xref:fundamentals/configuration/index) [seçenekler deseni](xref:fundamentals/configuration/options)kullanılarak enjekte edilebilir, bir , bir `string`gibi yerleşik bir [tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektirir:

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın oluşturucusu aracılığıyla istenir. Alan, sınıf boyunca gerektiğinde hizmete erişmek için kullanılır.

Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini aramak için kullanılır:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Başlangıç'a enjekte edilen hizmetler

Genel Ana Bilgisayar kullanırken `Startup` oluşturucuya yalnızca aşağıdaki hizmet<xref:Microsoft.Extensions.Hosting.IHostBuilder>türleri enjekte edilebilir ( ):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Hizmetler enjekte `Startup.Configure`edilebilir:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Çerçeve tarafından sağlanan hizmetler

Yöntem, `Startup.ConfigureServices` Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur. Başlangıçta, `IServiceCollection` sağlanan `ConfigureServices` hizmetler [ana bilgisayar yapılandırılmıştır nasıl](xref:fundamentals/index#host)bağlı olarak çerçeve tarafından tanımlanan vardır. ASP.NET Core şablonuna dayalı bir uygulamanın çerçeve tarafından yüzlerce hizmetin kaydolması nadir değildir. Çerçeveye kayıtlı hizmetlerin küçük bir örneği aşağıdaki tabloda listelenmiştir.

| Hizmet Türü | Ömür |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Geçi -ci |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Geçi -ci |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Geçi -ci |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Geçi -ci |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Ek hizmetleri genişletme yöntemleriyle kaydetme

Bir hizmeti kaydetmek için bir hizmet toplama uzantısı yöntemi (ve gerekirse bağımlı hizmetleri) `Add{SERVICE_NAME}` kullanılabilir olduğunda, sözleşme, söz le ilgili hizmetin gerektirdiği tüm hizmetleri kaydetmek için tek bir uzantı yöntemi kullanmaktır. Aşağıdaki kod, [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ve aşağıdaki uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetlerin nasıl ekleyeceğinize bir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>örnektir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Daha fazla bilgi <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> için API belgelerindeki sınıfa bakın.

## <a name="service-lifetimes"></a>Hizmet ömürleri

Her kayıtlı hizmet için uygun bir ömür seçin. ASP.NET Core hizmetleri aşağıdaki yaşam süreleriyle yapılandırılabilir:

### <a name="transient"></a>Geçi -ci

Geçici yaşam süresi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>hizmetleri ( ) hizmet kapsayıcısından her istendiğinde oluşturulur. Bu ömür, hafif ve devletsiz hizmetler için en iyi şekilde çalışır.

### <a name="scoped"></a>Kapsamlı

Kapsamlı yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>süresi hizmetleri ( ) istemci isteği (bağlantı) başına bir kez oluşturulur.

> [!WARNING]
> Bir ara yazılımda kapsamlı bir hizmet kullanırken, `Invoke` `InvokeAsync` hizmeti veya yöntemini enjekte edin. Yapıcı enjeksiyon yoluyla enjekte etmeyin çünkü hizmeti tek ton gibi olmaya zorlar. Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Singleton yaşam<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>boyu hizmetleri ( ) ilk kez istendiğinde `Startup.ConfigureServices` oluşturulur (veya çalıştırıldığında ve hizmet kaydıyla bir örnek belirtilir). Sonraki her istek aynı örneği kullanır. Uygulama singleton davranışı gerektiriyorsa, hizmet kapsayıcısının hizmetin kullanım ömrünü yönetmesine izin vermek önerilir. Singleton tasarım deseni uygulamayın ve nesnenin sınıftaki ömrünü yönetmek için kullanıcı kodu sağlamayın.

> [!WARNING]
> Tek tonlu bir hizmeti çözmek tehlikelidir. Sonraki istekleri işlerken hizmetin yanlış duruma sahip olmasına neden olabilir.

## <a name="service-registration-methods"></a>Hizmet kayıt yöntemleri

Hizmet kaydı uzatma yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.

| Yöntem | Automatic<br>object<br>Elden çıkarma | Birden çok<br>uygulamalar | Pass args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<IMyDep, MyDep>();` | Evet | Evet | Hayır |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Evet | Evet | Evet |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<MyDep>();` | Evet | Hayır | Hayır |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Hayır | Evet | Evet |
| `AddSingleton(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Hayır | Hayır | Evet |

Tür elden çıkarma hakkında daha fazla bilgi [için, hizmetlerin Elden Çıkarılması](#disposal-of-services) bölümüne bakın. Birden çok uygulama için ortak bir [senaryo, sınama türleri ile alay ediyor.](xref:test/integration-tests#inject-mock-services)

`TryAdd{LIFETIME}`yöntemler yalnızca kayıtlı bir uygulama yoksa hizmeti kaydedin.

Aşağıdaki örnekte, ilk satır `MyDependency` için `IMyDependency`kaydeder. `IMyDependency` Zaten kayıtlı bir uygulama olduğundan ikinci satırın hiçbir etkisi yoktur:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Daha fazla bilgi için bkz.

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder. Birden çok hizmet `IEnumerable<{SERVICE}>`. Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri henüz eklenmemişse bir örnek eklemek ister. Genellikle, bu yöntem kapsayıcıda bir örneğin iki kopyasını kaydetmeyi önlemek için kitaplık yazarları tarafından kullanılır.

Aşağıdaki örnekte, ilk satır `MyDep` için `IMyDep1`kaydeder. İkinci satır `MyDep` için `IMyDep2`kaydeder. Zaten kayıtlı bir uygulama `IMyDep1` vardır, çünkü `MyDep`üçüncü satırın hiçbir etkisi yoktur:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Yapıcı enjeksiyon davranışı

Hizmetler iki mekanizma yla çözülebilir:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık enjeksiyon konteynerinde hizmet kaydı olmadan nesne oluşturmaya izin verir. `ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model bağlayıcıları gibi kullanıcıya yönelik soyutlamalarla kullanılır.

Oluşturucular bağımlılık enjeksiyonu tarafından sağlanmadı bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenler varsayılan değerleri atamalıdır.

Hizmetler tarafından `IServiceProvider` çözüldüğünde `ActivatorUtilities`veya , yapıcı enjeksiyon bir *kamu* yapıcı gerektirir.

Hizmetler tarafından `ActivatorUtilities`çözüldüğünde, yapıcı enjeksiyon sadece bir uygulanabilir yapıcı var gerektirir. Yapıcı aşırı yükler desteklenir, ancak bağımsız değişkenleri bağımlılık enjeksiyonu yla yerine getirilebilen yalnızca bir aşırı yükleme olabilir.

## <a name="entity-framework-contexts"></a>Varlık Çerçevesi bağlamları

Varlık Çerçevesi bağlamları genellikle [kapsamlı yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir, çünkü web uygulaması veritabanı işlemleri normalde istemci isteğine yöneliktir. Veritabanı bağlamını kaydederken bir [adddbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklenme tarafından bir ömür belirtilmemişse varsayılan yaşam süresi kapsamlıdır. Belirli bir yaşam süresinin hizmetleri, hizmetten daha kısa bir kullanım ömrüne sahip bir veritabanı bağlamı kullanmamalıdır.

## <a name="lifetime-and-registration-options"></a>Ömür boyu ve kayıt seçenekleri

Kullanım ömrü ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir `OperationId`işlem olarak temsil eden aşağıdaki arabirimleri düşünün. Bir işlem hizmetinin kullanım ömrünün aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak, kapsayıcı bir sınıf tarafından istendiğinde hizmetin aynı veya farklı bir örneğini sağlar:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Arabirimler `Operation` sınıfta uygulanır. Oluşturucu, `Operation` sağlanmazsa bir GUID oluşturur:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Diğer `OperationService` `Operation` türlerin her birine bağlı olarak bir kayıtlıdır. Bağımlılık `OperationService` enjeksiyonu yoluyla istendiğinde, bağımlı hizmetin kullanım ömrüne bağlı olarak her hizmetin yeni bir örneğini veya varolan bir örneği alır.

* Kapsayıcıdan istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmetin .'den `OperationId` farkı `OperationService`vardır. `OperationService`sınıfın yeni bir `IOperationTransient` örneğini alır. Yeni örnek farklı `OperationId`bir verim verir.
* Kapsamlı hizmetler istemci isteği başına `OperationId` oluşturulduğunda, `IOperationScoped` hizmetin istemci `OperationService` isteği içindekiyle aynıdır. İstemci istekleri arasında, `OperationId` her iki hizmet de farklı bir değeri paylaşır.
* Singleton ve singleton-instance hizmetleri bir kez oluşturulduğunda ve tüm `OperationId` istemci istekleri ve tüm hizmetler arasında kullanıldığında, tüm hizmet istekleri arasında sabittir.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

Her `Startup.ConfigureServices`tür, kapta adlandırılmış ömrüne göre eklenir:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Hizmet, `IOperationSingletonInstance` bilinen bir kimliği olan belirli `Guid.Empty`bir örneği kullanıyor. Bu tür kullanımda olduğunda açıktır (ONUN GUID tüm sıfırlar).

Örnek uygulama, tek tek istekler içinde ve arasında nesne yaşam ömürlerini gösterir. Örnek uygulamanın `IndexModel` her `IOperation` tür ve `OperationService`. Sayfa daha sonra özellik atamaları aracılığıyla sayfa modeli `OperationId` sınıfının ve hizmet değerlerinin tümlerini görüntüler:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Aşağıdaki iki çıktı iki isteğin sonuçlarını gösterir:

**İlk istek:**

Denetleyici işlemleri:

Geçici: d233e165-f417-469b-a866-1cf1935d2518  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

`OperationService`Işlem:

Geçici: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

**İkinci istek:**

Denetleyici işlemleri:

Geçici: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

`OperationService`Işlem:

Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek kişilik: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-0000000000000

Değerlerden hangisinin `OperationId` istek içinde ve istekler arasında farklılık gösterdiğini gözlemleyin:

* *Geçici* nesneler her zaman farklıdır. Hem birinci `OperationId` hem de ikinci istemci istekleri için `OperationService` geçici değer hem işlemler hem de istemci istekleri arasında farklıdır. Her hizmet isteği ve istemci isteği için yeni bir örnek sağlanır.
* *Kapsamlı* nesneler istemci isteği içinde aynıdır, ancak istemci istekleri arasında farklıdır.
* *Singleton* nesneleri, bir `Operation` örneğin sağlanıp sağlanmadığına bakılmaksızın her `Startup.ConfigureServices`nesne ve her istek için aynıdır.

## <a name="call-services-from-main"></a>Ana hizmetten çağrı hizmetleri

Uygulama <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> kapsamındaki kapsamlı bir hizmeti çözmek için [iServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir uygulama oluşturun. Bu yaklaşım, başlatma görevlerini çalıştırmak için başlangıçta kapsamlı bir hizmete erişmek için yararlıdır. Aşağıdaki örnek, aşağıdaki `MyScopedService` `Program.Main`için bir bağlam nasıl elde edilebildiğini gösterir:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a>Kapsam doğrulama

Uygulama Geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı aşağıdakileri doğrulamak için denetimler gerçekleştirir:

* Kapsamlı hizmetler, kök hizmet sağlayıcısından doğrudan veya dolaylı olarak çözülmez.
* Kapsamlı hizmetler doğrudan veya dolaylı olarak singleton içine enjekte değildir.

Kök hizmet sağlayıcısı çağrıldığında <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> oluşturulur. Kök servis sağlayıcısının ömrü, sağlayıcı nın uygulamayla birlikte işe başlaması ve uygulama kapandığında imha edilmesiyle uygulamanın/sunucunun ömrüne karşılık gelir.

Kapsamlı hizmetler, bunları oluşturan kapsayıcı tarafından bertaraf edilir. Kök kapsayıcıda kapsamlı bir hizmet oluşturulursa, yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından bertaraf edildiğinden, hizmetin ömrü etkin bir şekilde singleton'a yükseltilir. Hizmet kapsamlarını doğrulama çağrıldığında `BuildServiceProvider` bu durumları yakalar.

Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Talep Hizmetleri

ASP.NET Core isteği içinde sunulan `HttpContext` [hizmetler, HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla açıklanır.

İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil ediyor. Nesneler bağımlılıkları belirttiğinde, bunlar , `RequestServices` `ApplicationServices`değil' de bulunan türlerle karşılanır.

Genellikle, uygulama bu özellikleri doğrudan kullanmamalıdır. Bunun yerine, sınıf oluşturucular aracılığıyla sınıfların gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları enjekte etmesine izin verin. Bu, sınaması daha kolay olan sınıflar verir.

> [!NOTE]
> Koleksiyona erişmek için kurucu parametreleri olarak `RequestServices` bağımlılıkları talep etmeyi tercih edin.

## <a name="design-services-for-dependency-injection"></a>Bağımlılık enjeksiyonu için tasarım hizmetleri

En iyi uygulamalar şunlardır:

* Bağımlılıklarını elde etmek için bağımlılık enjeksiyonu kullanmak için tasarım hizmetleri.
* Durum lu, statik sınıflardan ve üyelerden kaçının. Bunun yerine, küresel durum oluşturmaktan kaçınan singleton hizmetlerini kullanmak için uygulamalar tasarlayın.
* Hizmetler içindeki bağımlı sınıfların doğrudan anında anlanmasından kaçının. Kodu belirli bir uygulamaya yönlendirin anlık çiftler.
* Uygulama sınıflarını küçük, iyi faktörlü ve kolayca test edilebilen hale getirin.

Bir sınıfın çok fazla enjekte edilmiş bağımlılığı varsa, bu genellikle sınıfın çok fazla sorumluluğu olduğunun ve [Tek Sorumluluk İlkesi'ni (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal ettiğini gösteren bir işarettir. Bazı sorumluluklarını yeni bir sınıfa taşıyarak sınıfı yeniden düzenlemeyi dene. Razor Pages sayfa modeli sınıflarının ve MVC denetleyici sınıflarının UI endişelerine odaklanması gerektiğini unutmayın. İş kuralları ve veri erişimi uygulama ayrıntıları bu [ayrı endişelere](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.

### <a name="disposal-of-services"></a>Hizmetlerin bertaraf edilmesi

Kapsayıcı, <xref:System.IDisposable.Dispose*> oluşturduğu <xref:System.IDisposable> türleri çağırır. Kullanıcı koduyla kapsayıcıya bir örnek eklenirse, otomatik olarak atılmaz.

Aşağıdaki örnekte, hizmetler servis kapsayıcısı tarafından oluşturulur ve otomatik olarak atılır:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

Aşağıdaki örnekte:

* Hizmet örnekleri servis kapsayıcısı tarafından oluşturulmaz.
* Amaçlanan hizmet ömürleri çerçeve tarafından bilinmemektedir.
* Çerçeve hizmetleri otomatik olarak elden çıkarmaz.
* Hizmetler geliştirici kodunda açıkça belirtinmezse, uygulama kapanana kadar devam eder.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a>Varsayılan hizmet kapsayıcısı değiştirme

Dahili servis konteyneri, çerçevenin ve çoğu tüketici uygulamasının ihtiyaçlarına hizmet etmek üzere tasarlanmıştır. Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız olmadığı sürece yerleşik kapsayıcıyı kullanmanızı öneririz:

* Özellik enjeksiyonu
* İsme göre enjeksiyon
* Çocuk kapları
* Özel yaşam boyu yönetimi
* `Func<T>`tembel başlatma desteği
* Sözleşme tabanlı kayıt

Aşağıdaki 3 ASP.NET.

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Dryioc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Zarafet](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Zula kutusu](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>İş parçacığı güvenliği

İş parçacığı güvenli singleton hizmetleri oluşturun. Bir singleton hizmetigeçici bir hizmete bağımlıysa, geçici hizmet, singleton tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.

[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)ikinci bağımsız değişkeni gibi tek hizmet fabrika yöntemi, iş parçacığı güvenli olması gerekmez. Bir tür`static`gibi ( ) oluşturucu, tek bir iş parçacığı tarafından bir kez çağrılması garanti.

## <a name="recommendations"></a>Öneriler

* `async/await`ve `Task` tabanlı hizmet çözünürlüğü desteklenmez. C# asynchronous yapıcıları desteklemez; bu nedenle, önerilen desen eşitolarak hizmeti çözdükten sonra eşzamanlı yöntemler kullanmaktır.

* Verileri ve yapılandırmayı doğrudan servis kapsayıcısında depolamaktan kaçının. Örneğin, bir kullanıcının alışveriş sepeti genellikle servis kapsayıcısına eklenmemelidir. Yapılandırma seçenekleri [deseni](xref:fundamentals/configuration/options)kullanmalıdır. Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için var olan "veri tutucu" nesnelerden kaçının. Bu DI üzerinden gerçek öğeyi istemek daha iyidir.

* Hizmetlere statik erişimden kaçının (örneğin, başka bir yerde kullanmak için statik olarak [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) yazarak).

* Denetim stratejilerinin [Ters Çevrilmesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) karıştıran *hizmet bulucu deseni*kullanmaktan kaçının.

  * Bunun yerine <xref:System.IServiceProvider.GetService*> DI'yi kullanabileceğiniz bir hizmet örneği almak için çağrıda almayın:

    **Yanlış:**

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    **Doğru**:

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * Kullanarak çalışma zamanında bağımlılıkları çözen bir <xref:System.IServiceProvider.GetService*>fabrika enjekte kaçının.

* Statik erişimden `HttpContext` kaçının (örneğin, [IHttpContextAccessor.HttpContext).](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)

Tüm öneri kümeleri gibi, bir öneriyi yoksaymanın gerekli olduğu durumlarla karşılaşabilirsiniz. İstisnalar, çerçevenin kendisi içinde nadir görülen&mdash;özel durumlardır.

DI statik /küresel nesne erişim desenleri için bir *alternatiftir.* Statik nesne erişimiyle karıştırırsanız DI'nin faydalarını fark edemeyebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Bağımlılık Enjeksiyonu (MSDN) ile ASP.NET Çekirdekte Temiz Kod Yazma](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Açık Bağımlılıklar İlkesi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Kontrol Kapları ve Bağımlılık Enjeksiyon Deseni (Martin Fowler) Ters](https://www.martinfowler.com/articles/injection.html)
* [Core DI'de birden çok arabirimi olan bir hizmet ASP.NET](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
