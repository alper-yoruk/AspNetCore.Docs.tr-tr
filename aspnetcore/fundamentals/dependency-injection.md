---
title: ASP.NET Core'da bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3e31be02f21f8c28c1d98d47d9a744b3a8502253
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003176"
---
# <a name="dependency-injection-in-aspnet-core"></a>ASP.NET Core'da bağımlılık ekleme

, [Steve Smith](https://ardalis.com/) ve [Scott Ade](https://scottaddie.com) tarafından

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.

MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection>..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Bağımlılık eklenmesine genel bakış

*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir. Aşağıdaki `MyDependency` sınıfı, bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle inceleyin:

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

Yöntemi bir sınıf için `MyDependency` kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir. `WriteMessage` `MyDependency` Sınıfı, `IndexModel` sınıfının bir bağımlılığı olur:

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

Sınıf oluşturur ve `MyDependency` örneğe doğrudan bağlıdır. Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:

* Farklı bir `MyDependency` uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.
* `MyDependency` Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir. Uygulamasına bağlı olarak `MyDependency`, birden çok sınıfı olan büyük bir projede yapılandırma kodu uygulama genelinde dağılmış hale gelir.
* Bu uygulamanın birim testi zordur. Uygulamanın bu yaklaşım ile mümkün olmayan bir sahte `MyDependency` veya saplama sınıfı kullanması gerekir.

Bağımlılık ekleme bu sorunları şu şekilde giderir:

* Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.
* Bir hizmet kapsayıcısına bağımlılığın kaydı. ASP.NET Core, <xref:System.IServiceProvider>yerleşik bir hizmet kapsayıcısı sağlar. Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.
* Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* . Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.

[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Bu arabirim somut bir tür tarafından uygulanır, `MyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`kendi oluşturucusunda <xref:Microsoft.Extensions.Logging.ILogger`1> bir ister. Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir. Her istenen bağımlılık, kendi bağımlılıklarını ister. Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür. Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.

`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır. `IMyDependency`kaydedilir `Startup.ConfigureServices`. `ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .

Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Örnek uygulamada, `IMyDependency` hizmet somut tür `MyDependency`ile kaydedilir. Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar. [Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır). Örneğin, `services.AddMvc()` Razor Pages ve MVC 'nin gerektirdiği Hizmetleri ekler. Uygulamaların bu kuralı izlemesini öneririz. Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.

Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`gerektiriyorsa, tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:

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

Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir. Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.

Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini çağırmak için kullanılır:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Başlangıca eklenen hizmetler

Genel ana bilgisayar ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir:

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Hizmetler şu şekilde eklenebilir `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Framework tarafından sunulan hizmetler

`Startup.ConfigureServices` Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur. Başlangıçta, için `IServiceCollection` `ConfigureServices` belirtilen, [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir. Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur. Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.

| Hizmet Türü | Ömür |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Larsa |
| `IHostApplicationLifetime` | Adet |
| `IWebHostEnvironment` | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Larsa |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Adet |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Larsa |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Adet |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Adet |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Adet |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Larsa |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Adet |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Adet |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Adet |

## <a name="register-additional-services-with-extension-methods"></a>Uzantı yöntemleriyle ek hizmetleri kaydetme

Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek `Add{SERVICE_NAME}` bir genişletme yöntemi kullanmaktır. Aşağıdaki kod, [Adddbcontext\<tcontext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>

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

Daha fazla bilgi için API belgelerindeki <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> sınıfına bakın.

## <a name="service-lifetimes"></a>Hizmet yaşam süreleri

Kayıtlı her hizmet için uygun bir yaşam süresi seçin. ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:

### <a name="transient"></a>Larsa

Geçici ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>), hizmet kapsayıcısından her istenilişinde oluşturulur. Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.

### <a name="scoped"></a>Yayıl

Kapsamlı ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>), istemci isteği başına bir kez oluşturulur (bağlantı).

> [!WARNING]
> Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin. [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor. Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Adet

Tek yaşam süresi Hizmetleri<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() her istendiğinde oluşturulur (veya `Startup.ConfigureServices` çalıştırıldığında ve hizmet kaydıyla bir örnek belirtildiğinde). Her sonraki istek aynı örneği kullanır. Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir. Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.

> [!WARNING]
> Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir. Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.

## <a name="service-registration-methods"></a>Hizmet kayıt yöntemleri

Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.

| Yöntem | Automatic<br>nesne<br>elden | Birden çok<br>uygulamalar | Geçiş bağımsız değişkenleri |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<MyDep>();` | Yes | Hayır | Hayır |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes |
| `AddSingleton(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Hayır | Hayır | Yes |

Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın. Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.

Aşağıdaki örnekte, ilk satır için `MyDependency` `IMyDependency`kaydedilir. Zaten kayıtlı bir uygulamaya sahip olduğundan `IMyDependency` ikinci satır etkisizdir:

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

[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder. Aracılığıyla `IEnumerable<{SERVICE}>`birden çok hizmet çözümlenir. Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir. Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.

Aşağıdaki örnekte, ilk satır için `MyDep` `IMyDep1`kaydedilir. İkinci satır için `IMyDep2`kaydedilir `MyDep` . Zaten kayıtlı bir uygulamasına sahip olduğundan `IMyDep1` , üçüncü satırın etkisi yoktur: `MyDep`

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Oluşturucu Ekleme davranışı

Hizmetler, iki mekanizma tarafından çözülebilir:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir. `ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.

Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.

`IServiceProvider` Hizmetler veya `ActivatorUtilities`tarafından çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.

Hizmetler tarafından `ActivatorUtilities`çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir. Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.

## <a name="entity-framework-contexts"></a>Entity Framework bağlamları

Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir. Veritabanı bağlamı kaydedilirken aşırı yükleme [>bir\<adddbcontext tcontext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) tarafından belirtilmemişse, varsayılan yaşam süresi kapsamındadır. Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.

## <a name="lifetime-and-registration-options"></a>Ömür ve kayıt seçenekleri

Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId`. Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Arabirimler `Operation` sınıfında uygulanır. Bir `Operation` tane sağlanmazsa Oluşturucu bir GUID oluşturur:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService` , Diğer `Operation` türlerin her birine bağlı olarak kaydedilir. `OperationService` Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.

* Kapsayıcıda istendiğinde geçici `OperationId` hizmetler oluşturulduğunda, `IOperationTransient` hizmet öğesinden `OperationId` farklı olur. `OperationService` `OperationService``IOperationTransient` sınıfının yeni bir örneğini alır. Yeni örnek farklı `OperationId`bir şekilde oluşturur.
* İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği `OperationService` içindeki ile aynı olması gerekir. İstemci istekleri arasında her iki hizmet de farklı `OperationId` bir değer paylaşır.
* Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde `OperationId` kullanıldığında, tüm hizmet istekleri genelinde sabittir.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

' `Startup.ConfigureServices`De, her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance` Hizmet, bilinen kimliği olan belirli bir örnek kullanıyor `Guid.Empty`. Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.

Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir. Örnek uygulama, her `IndexModel` `IOperation` tür ve ' i ister `OperationService`. Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:

**İlk istek:**

Denetleyici işlemleri:

Geçici: d233e165-f417-469B-a866-1cf1935d2518  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationService`operasyonları

Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

**İkinci istek:**

Denetleyici işlemleri:

Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationService`operasyonları

Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationId` Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:

* *Geçici* nesneler her zaman farklıdır. Hem birinci `OperationId` hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır. Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.
* *Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.
* *Tek* nesneler, ' de `Operation` `Startup.ConfigureServices`bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır.

## <a name="call-services-from-main"></a>Ana bilgisayardan Hizmetleri çağır

Uygulamanın kapsamındaki <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun. Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır. Aşağıdaki örnek, `MyScopedService` içinde `Program.Main`için nasıl bağlam alınacağını gösterir:

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

## <a name="scope-validation"></a>Kapsam doğrulaması

Uygulama geliştirme ortamında çalışırken ve Konağı derlemek için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcı aşağıdakileri doğrulamak için denetimler gerçekleştirir:

* Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.
* Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.

Kök hizmet sağlayıcısı çağrıldığında oluşturulur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> . Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.

Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış. Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur. Hizmet kapsamlarını doğrulamak `BuildServiceProvider` , çağrıldığında bu durumları yakalar.

Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>İstek Hizmetleri

ASP.NET Core isteği `HttpContext` içinde bulunan hizmetler, [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.

İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder. Nesneler bağımlılıklar belirttiğinizde, bunlar ' de `RequestServices`bulunan türler tarafından karşılanır. `ApplicationServices`

Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır. Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin. Bu, test etmek daha kolay olan sınıfları oluşturur.

> [!NOTE]
> `RequestServices` Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin.

## <a name="design-services-for-dependency-injection"></a>Bağımlılık ekleme için tasarım hizmetleri

En iyi uygulamalar şunlardır:

* Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.
* Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının. Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.
* Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin. Doğrudan örnekleme kodu belirli bir uygulamaya bağar.
* Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.

Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır. Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi. Razor Pages sayfa modeli sınıfları ve MVC denetleyici sınıflarının UI kaygılarıyla odaklanıp ilgilenmeyeceğini aklınızda bulundurun. İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.

### <a name="disposal-of-services"></a>Hizmetlerin elden çıkarılması

Kapsayıcı, oluşturduğu <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> türleri çağırır. Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.

Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:

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

* Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.
* Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.
* Framework Hizmetleri otomatik olarak atmaz.
* Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

Hizmet verme seçeneklerinin bir tartışması için bkz. [ASP.NET Core ' de, Ndisposaveya ' ı atılamayan dört yol](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).

## <a name="default-service-container-replacement"></a>Varsayılan hizmet kapsayıcısı değiştirme

Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır. Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:

* Özellik ekleme
* Ada göre ekleme
* Alt kapsayıcılar
* Özel ömür yönetimi
* `Func<T>`yavaş başlatma desteği
* Kural tabanlı kayıt

Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Drıioc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Yetkisiz kullanım](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Açık Inject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [E](https://jasperfx.github.io/lamar/)
* [Stakıbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>İş parçacığı güvenliği

İş parçacığı güvenli Singleton Hizmetleri oluşturun. Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.

Tek bir hizmetin fabrika yöntemi (örneğin, [AddSingleton\<TService> için ikinci bağımsız değişken) (ıseviecollection,\<Func ıserviceprovider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez. Bir Type (`static`) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.

## <a name="recommendations"></a>Öneriler

* `async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez. C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.

* Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının. Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir. Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır. Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının. DI aracılığıyla gerçek öğe istemek daha iyidir.

* Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).

* *Hizmet bulucu deseninin*kullanmaktan kaçının. Örneğin, yerine şunu kullandığınızda <xref:System.IServiceProvider.GetService*> bir hizmet örneği elde etme çağrısı yapmayın:

  **Olmayan**

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

* Önlemek için başka bir hizmet bulucu çeşitlemesi, çalışma zamanında bağımlılıkları çözümleyen bir ekleme. Bu uygulamalardan her ikisi de [Denetim stratejilerini geçersiz kılar](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .

* Uygulamasına `HttpContext` statik erişimi önleyin (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz. Özel durumlar&mdash;genellikle çerçevenin kendisi içinde özel durumlardır.

Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* . Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>DI 'de çok kiracılı için önerilen desenler

[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , çok kiracılı bir uygulama sağlar. Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).

CMS 'e özgü özelliklerden herhangi https://github.com/OrchardCMS/OrchardCore.Samples biri olmadan yalnızca Orchard Core çerçevesini kullanarak modüler ve çok kiracılı uygulamalar oluşturmaya yönelik örnekler için adresindeki örnek uygulamalar bölümüne bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Açık bağımlılıklar Ilkesi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)](https://www.martinfowler.com/articles/injection.html)
* [ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.

MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection>..

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Bağımlılık eklenmesine genel bakış

*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir. Aşağıdaki `MyDependency` sınıfı, bir uygulamadaki diğer `WriteMessage` sınıfların bağlı olduğu bir yöntemle inceleyin:

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

Yöntemi bir sınıf için `MyDependency` kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir. `WriteMessage` `MyDependency` Sınıfı, `IndexModel` sınıfının bir bağımlılığı olur:

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

Sınıf oluşturur ve `MyDependency` örneğe doğrudan bağlıdır. Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:

* Farklı bir `MyDependency` uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.
* `MyDependency` Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir. Uygulamasına bağlı olarak `MyDependency`, birden çok sınıfı olan büyük bir projede yapılandırma kodu uygulama genelinde dağılmış hale gelir.
* Bu uygulamanın birim testi zordur. Uygulamanın bu yaklaşım ile mümkün olmayan bir sahte `MyDependency` veya saplama sınıfı kullanması gerekir.

Bağımlılık ekleme bu sorunları şu şekilde giderir:

* Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.
* Bir hizmet kapsayıcısına bağımlılığın kaydı. ASP.NET Core, <xref:System.IServiceProvider>yerleşik bir hizmet kapsayıcısı sağlar. Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.
* Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* . Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.

[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Bu arabirim somut bir tür tarafından uygulanır, `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`kendi oluşturucusunda <xref:Microsoft.Extensions.Logging.ILogger`1> bir ister. Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir. Her istenen bağımlılık, kendi bağımlılıklarını ister. Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür. Çözümlenmesi gereken, genellikle *bağımlılık ağacı*, *bağımlılık grafiği*veya *nesne grafiği*olarak adlandırılan toplu bağımlılıklar kümesi.

`IMyDependency`ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır. `IMyDependency`kaydedilir `Startup.ConfigureServices`. `ILogger<TCategoryName>`günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .

Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Örnek uygulamada, `IMyDependency` hizmet somut tür `MyDependency`ile kaydedilir. Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar. [Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır). Örneğin, `services.AddMvc()` Razor Pages ve MVC 'nin gerektirdiği Hizmetleri ekler. Uygulamaların bu kuralı izlemesini öneririz. Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.

Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`gerektiriyorsa, tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:

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

Hizmetin bir örneği, hizmetin kullanıldığı ve özel bir alana atandığı bir sınıfın Oluşturucusu aracılığıyla istenir. Alanı, sınıfına gereken şekilde hizmete erişmek için kullanılır.

Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin `WriteMessage` yöntemini çağırmak için kullanılır:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Başlangıca eklenen hizmetler

Genel ana bilgisayar ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir:

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Hizmetler şu şekilde eklenebilir `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Framework tarafından sunulan hizmetler

`Startup.ConfigureServices` Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur. Başlangıçta, için `IServiceCollection` `ConfigureServices` belirtilen, [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir. Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur. Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.

| Hizmet Türü | Ömür |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Larsa |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Larsa |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Adet |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Larsa |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Adet |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Adet |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Adet |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Larsa |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Adet |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Adet |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Adet |

## <a name="register-additional-services-with-extension-methods"></a>Uzantı yöntemleriyle ek hizmetleri kaydetme

Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek `Add{SERVICE_NAME}` bir genişletme yöntemi kullanmaktır. Aşağıdaki kod, [Adddbcontext\<tcontext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>

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

Daha fazla bilgi için API belgelerindeki <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> sınıfına bakın.

## <a name="service-lifetimes"></a>Hizmet yaşam süreleri

Kayıtlı her hizmet için uygun bir yaşam süresi seçin. ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:

### <a name="transient"></a>Larsa

Geçici ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>), hizmet kapsayıcısından her istenilişinde oluşturulur. Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.

### <a name="scoped"></a>Yayıl

Kapsamlı ömür Hizmetleri (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>), istemci isteği başına bir kez oluşturulur (bağlantı).

> [!WARNING]
> Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin. [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor. Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Adet

Tek yaşam süresi Hizmetleri<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() her istendiğinde oluşturulur (veya `Startup.ConfigureServices` çalıştırıldığında ve hizmet kaydıyla bir örnek belirtildiğinde). Her sonraki istek aynı örneği kullanır. Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir. Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.

> [!WARNING]
> Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir. Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.

## <a name="service-registration-methods"></a>Hizmet kayıt yöntemleri

Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.

| Yöntem | Automatic<br>nesne<br>elden | Birden çok<br>uygulamalar | Geçiş bağımsız değişkenleri |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | Hayır |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<MyDep>();` | Yes | Hayır | Hayır |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes |
| `AddSingleton(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Hayır | Hayır | Yes |

Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın. Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`Yöntemler, zaten kayıtlı bir uygulama yoksa hizmeti kaydeder.

Aşağıdaki örnekte, ilk satır için `MyDependency` `IMyDependency`kaydedilir. Zaten kayıtlı bir uygulamaya sahip olduğundan `IMyDependency` ikinci satır etkisizdir:

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

[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde*bir uygulama yoksa hizmeti kaydeder. Aracılığıyla `IEnumerable<{SERVICE}>`birden çok hizmet çözümlenir. Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir. Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.

Aşağıdaki örnekte, ilk satır için `MyDep` `IMyDep1`kaydedilir. İkinci satır için `IMyDep2`kaydedilir `MyDep` . Zaten kayıtlı bir uygulamasına sahip olduğundan `IMyDep1` , üçüncü satırın etkisi yoktur: `MyDep`

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Oluşturucu Ekleme davranışı

Hizmetler, iki mekanizma tarafından çözülebilir:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir. `ActivatorUtilities`Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.

Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.

`IServiceProvider` Hizmetler veya `ActivatorUtilities`tarafından çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.

Hizmetler tarafından `ActivatorUtilities`çözümlendiğinde, [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir. Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.

## <a name="entity-framework-contexts"></a>Entity Framework bağlamları

Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir. Veritabanı bağlamı kaydedilirken aşırı yükleme [>bir\<adddbcontext tcontext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) tarafından belirtilmemişse, varsayılan yaşam süresi kapsamındadır. Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.

## <a name="lifetime-and-registration-options"></a>Ömür ve kayıt seçenekleri

Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId`. Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Arabirimler `Operation` sınıfında uygulanır. Bir `Operation` tane sağlanmazsa Oluşturucu bir GUID oluşturur:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService` , Diğer `Operation` türlerin her birine bağlı olarak kaydedilir. `OperationService` Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.

* Kapsayıcıda istendiğinde geçici `OperationId` hizmetler oluşturulduğunda, `IOperationTransient` hizmet öğesinden `OperationId` farklı olur. `OperationService` `OperationService``IOperationTransient` sınıfının yeni bir örneğini alır. Yeni örnek farklı `OperationId`bir şekilde oluşturur.
* İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği `OperationService` içindeki ile aynı olması gerekir. İstemci istekleri arasında her iki hizmet de farklı `OperationId` bir değer paylaşır.
* Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde `OperationId` kullanıldığında, tüm hizmet istekleri genelinde sabittir.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

' `Startup.ConfigureServices`De, her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance` Hizmet, bilinen kimliği olan belirli bir örnek kullanıyor `Guid.Empty`. Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.

Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir. Örnek uygulama, her `IndexModel` `IOperation` tür ve ' i ister `OperationService`. Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:

**İlk istek:**

Denetleyici işlemleri:

Geçici: d233e165-f417-469B-a866-1cf1935d2518  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationService`operasyonları

Geçici: c6b049eb-1318-4E31-90f1-eb2dd849ff64  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

**İkinci istek:**

Denetleyici işlemleri:

Geçici: b63bd538-0a37-4FF1-90ba-081c5138dda0  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationService`operasyonları

Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationId` Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:

* *Geçici* nesneler her zaman farklıdır. Hem birinci `OperationId` hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır. Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.
* *Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.
* *Tek* nesneler, ' de `Operation` `Startup.ConfigureServices`bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır.

## <a name="call-services-from-main"></a>Ana bilgisayardan Hizmetleri çağır

Uygulamanın kapsamındaki <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun. Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır. Aşağıdaki örnek, `MyScopedService` içinde `Program.Main`için nasıl bağlam alınacağını gösterir:

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

## <a name="scope-validation"></a>Kapsam doğrulaması

Uygulama geliştirme ortamında çalışırken, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:

* Kapsamlı hizmetler doğrudan veya dolaylı olarak kök hizmet sağlayıcısından çözümlenmez.
* Kapsamlı hizmetler doğrudan veya dolaylı olarak Singleton 'a eklenmiş değildir.

Kök hizmet sağlayıcısı çağrıldığında oluşturulur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> . Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.

Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış. Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur. Hizmet kapsamlarını doğrulamak `BuildServiceProvider` , çağrıldığında bu durumları yakalar.

Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>İstek Hizmetleri

ASP.NET Core isteği `HttpContext` içinde bulunan hizmetler, [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.

İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder. Nesneler bağımlılıklar belirttiğinizde, bunlar ' de `RequestServices`bulunan türler tarafından karşılanır. `ApplicationServices`

Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır. Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin. Bu, test etmek daha kolay olan sınıfları oluşturur.

> [!NOTE]
> `RequestServices` Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin.

## <a name="design-services-for-dependency-injection"></a>Bağımlılık ekleme için tasarım hizmetleri

En iyi uygulamalar şunlardır:

* Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.
* Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının. Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.
* Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin. Doğrudan örnekleme kodu belirli bir uygulamaya bağar.
* Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.

Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır. Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi. Sayfaların sayfa modeli sınıflarının Razor ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın. İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.

### <a name="disposal-of-services"></a>Hizmetlerin elden çıkarılması

Kapsayıcı, oluşturduğu <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> türleri çağırır. Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.

Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:

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

* Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.
* Hedeflenen hizmet yaşam süreleri Framework tarafından bilinmemektedir.
* Framework Hizmetleri otomatik olarak atmaz.
* Hizmetler, geliştirici kodunda açıkça atılmadıklarında, uygulama kapatılıncaya kadar kalır.

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

Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır. Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:

* Özellik ekleme
* Ada göre ekleme
* Alt kapsayıcılar
* Özel ömür yönetimi
* `Func<T>`yavaş başlatma desteği
* Kural tabanlı kayıt

Aşağıdaki 3. taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Drıioc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Yetkisiz kullanım](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Açık Inject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [E](https://jasperfx.github.io/lamar/)
* [Stakıbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>İş parçacığı güvenliği

İş parçacığı güvenli Singleton Hizmetleri oluşturun. Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.

Tek bir hizmetin fabrika yöntemi (örneğin, [AddSingleton\<TService> için ikinci bağımsız değişken) (ıseviecollection,\<Func ıserviceprovider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez. Bir Type (`static`) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.

## <a name="recommendations"></a>Öneriler

* `async/await`ve `Task` tabanlı hizmet çözümlemesi desteklenmez. C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.

* Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının. Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir. Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır. Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının. DI aracılığıyla gerçek öğe istemek daha iyidir.

* Hizmetlere statik erişimi önleyin (örneğin, başka bir yerde kullanmak üzere, statik olarak yazılan [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ).

* [Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin*kullanmaktan kaçının.

  * Bunun yerine <xref:System.IServiceProvider.GetService*> , şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:

    **Olmayan**

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

  * Kullanarak <xref:System.IServiceProvider.GetService*>çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin.

* Uygulamasına `HttpContext` statik erişimi önleyin (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz. Özel durumlar&mdash;genellikle çerçevenin kendisi içinde özel durumlardır.

Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* . Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Açık bağımlılıklar Ilkesi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)](https://www.martinfowler.com/articles/injection.html)
* [ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
