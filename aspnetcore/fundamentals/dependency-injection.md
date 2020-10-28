---
title: ASP.NET Core bağımlılık ekleme
author: rick-anderson
description: ASP.NET Core bağımlılık ekleme ve nasıl kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678571"
---
# <a name="dependency-injection-in-aspnet-core"></a>ASP.NET Core bağımlılık ekleme

::: moniker range=">= aspnetcore-3.0"

[Kirk Larkabağı](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.

MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..

Konsol uygulamalarında bağımlılık ekleme hakkında daha fazla bilgi için bkz. [.net 'Te bağımlılık ekleme](/dotnet/core/extensions/dependency-injection)

Seçeneklerin bağımlılığı ekleme hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/options> ..

Bu konuda ASP.NET Core bağımlılık ekleme hakkında bilgi verilmektedir. Konsol uygulamalarında bağımlılık ekleme hakkında daha fazla bilgi için bkz. [.net 'e bağımlılık ekleme](/dotnet/core/extensions/dependency-injection).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Bağımlılık eklenmesine genel bakış

*Bağımlılık* , başka bir nesnenin bağımlı olduğu bir nesnedir. Aşağıdaki sınıfı, `MyDependency` `WriteMessage` diğer sınıfların bağımlı olduğu bir yöntemle inceleyin:

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

Bir sınıf, `MyDependency` yöntemini kullanmak için sınıfının bir örneğini oluşturabilir `WriteMessage` . Aşağıdaki örnekte, `MyDependency` sınıfı sınıfının bir bağımlılığı olur `IndexModel` :

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

Sınıfı oluşturur ve doğrudan `MyDependency` sınıfa bağlıdır. Önceki örnekte olduğu gibi kod bağımlılıkları sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:

* `MyDependency`Farklı bir uygulamayla değiştirmek için, `IndexModel` sınıfın değiştirilmesi gerekir.
* `MyDependency`Bağımlılıkları varsa, sınıf tarafından da yapılandırılmalıdır `IndexModel` . Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.
* Bu uygulamanın birim testi zordur. Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.

Bağımlılık ekleme bu sorunları şu şekilde giderir:

* Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.
* Bir hizmet kapsayıcısına bağımlılığın kaydı. ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> . Hizmetler genellikle uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.
* Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* . Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.

[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirimi `WriteMessage` yöntemini tanımlar:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

Örnek uygulama, `IMyDependency` hizmeti somut tür ile kaydeder `MyDependency` . <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Yöntemi, hizmeti tek bir isteğin ömrü olan kapsamlı bir yaşam süresine kaydeder. [Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

Örnek uygulamada, `IMyDependency` hizmet istenir ve yöntemi çağırmak için kullanılır `WriteMessage` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

DI modelini kullanarak denetleyici:

* Somut türü kullanmaz `MyDependency` , yalnızca `IMyDependency` uyguladığı arabirim. Bu, denetleyicinin, denetleyiciyi değiştirmeden kullandığı uygulamayı değiştirmeyi kolaylaştırır.
* Bir örneği oluşturmaz `MyDependency` , bu, dı kapsayıcısı tarafından oluşturulur.

Arabirim uygulanması, `IMyDependency` yerleşik günlük API 'si kullanılarak artırılabilir:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

Updated `ConfigureServices` yöntemi yeni `IMyDependency` uygulamayı kaydeder:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger%601>, oluşturucuda istediği öğesine bağlıdır. `ILogger<TCategoryName>`[Framework tarafından sağlanmış bir hizmettir](#framework-provided-services).

Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir. Her istenen bağımlılık, kendi bağımlılıklarını ister. Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür. Çözümlenmesi gereken, genellikle *bağımlılık ağacı* , *bağımlılık grafiği* veya *nesne grafiği* olarak adlandırılan toplu bağımlılıklar kümesi.

Kapsayıcı, `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır.

Bağımlılık ekleme terminolojisi ' nde bir hizmet:

* Genellikle hizmet gibi diğer nesnelere hizmet sağlayan bir nesnedir `IMyDependency` .
* Bir Web hizmetiyle ilgili değildir, ancak hizmet bir Web hizmeti kullanabilir.

Çerçeve, güçlü bir [günlük](xref:fundamentals/logging/index) sistemi sağlar. `IMyDependency`Yukarıdaki örneklerde gösterilen uygulamalar, günlüğü uygulamamak için değil temel dı 'yi göstermek için yazılmıştır. Çoğu uygulamanın Günlükçüler yazması gerekmez. Aşağıdaki kod, bir hizmetin kaydedilmesini gerektirmeyen varsayılan günlük kaydını kullanmayı göstermektedir `ConfigureServices` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

Yukarıdaki kodu kullanarak, `ConfigureServices` Framework tarafından [günlüğe kaydetme](xref:fundamentals/logging/index) sağlandığı için güncelleştirmeniz gerekmez.

## <a name="services-injected-into-startup"></a>Başlangıca eklenen hizmetler

Hizmetler `Startup` oluşturucuya ve `Startup.Configure` metoduna eklenebilir.

`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmetler eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Dı kapsayıcısına kayıtlı tüm hizmetler `Startup.Configure` metoduna eklenebilir:

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

Daha fazla bilgi için, bkz <xref:fundamentals/startup> . ve [erişim yapılandırması başlangıç](xref:fundamentals/configuration/index#access-configuration-in-startup).

## <a name="register-groups-of-services-with-extension-methods"></a>Uzantı yöntemleriyle hizmet gruplarını kaydet

ASP.NET Core Framework, bir grup ilgili hizmeti kaydetmek için bir kural kullanır. Kural, `Add{GROUP_NAME}` bir Framework özelliği için gereken tüm hizmetleri kaydetmek için tek bir genişletme yöntemi kullanmaktır. Örneğin, Microsoft. Extensions. Dependencyınjection. MvcServiceCollectionExtensions. AddControllers> genişletme yöntemi <MVC denetleyicileri için gereken hizmetleri kaydeder.

Aşağıdaki kod, Razor bireysel kullanıcı hesapları kullanılarak sayfalar şablonu tarafından oluşturulur ve uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemeyi gösterir <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ve <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>Hizmet yaşam süreleri

Hizmetler aşağıdaki yaşam sürelerinin biriyle kaydedilebilir:

* Larsa
* Yayıl
* Adet

Aşağıdaki bölümlerde, önceki yaşam sürelerinin her biri açıklanır. Kayıtlı her hizmet için uygun bir yaşam süresi seçin. 

### <a name="transient"></a>Larsa

Geçici ömür Hizmetleri, hizmet kapsayıcısından her istenilişinde oluşturulur. Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır. Geçici Hizmetleri ile kaydedin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .

İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.

### <a name="scoped"></a>Yayıl

Kapsamlı ömür Hizmetleri, istemci isteği başına bir kez oluşturulur (bağlantı). Kapsamlı hizmetleri ile kaydedin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .

İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.

Entity Framework Core kullanılırken, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> genişletme yöntemi, `DbContext` Varsayılan olarak kapsamlı yaşam süresine sahip türleri kaydeder.

Tek bir kapsamdaki hizmeti bir tekil hizmetten çözümleyin ve örneğin geçici bir hizmet aracılığıyla dolaylı olarak değil, bunun gibi bir **şekilde değil.** Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir. Şunları yapabilirsiniz:

_ Kapsamlı veya geçici bir hizmetten tek bir hizmeti çözümleyin.
* Kapsamlı bir hizmeti başka bir kapsamlı veya geçici hizmetten çözün.

Varsayılan olarak, geliştirme ortamında, bir hizmetin daha uzun bir yaşam süresine sahip başka bir hizmetten çözülmesi bir özel durum oluşturur. Daha fazla bilgi için bkz. [kapsam doğrulaması](#sv).

Ara yazılım kapsamındaki hizmetleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:

* Hizmeti, ara yazılımı `Invoke` veya `InvokeAsync` yöntemine ekleyin. [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) kullanılması, kapsamlı hizmeti tek bir gibi davranmaya zordığı için bir çalışma zamanı özel durumu oluşturur. [Ömür ve kayıt seçenekleri](#lifetime-and-registration-options) bölümündeki örnek, `InvokeAsync` yaklaşımı gösterir.
* [Fabrika tabanlı ara yazılım](xref:fundamentals/middleware/extensibility)kullanın. Bu yaklaşım kullanılarak kaydedilen ara yazılım, kapsamlı hizmetlerin ara yazılım yöntemine eklenmiş olmasına olanak tanıyan istemci isteği (bağlantı) başına etkinleştirilir `InvokeAsync` .

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Adet

Tek yaşam süresi Hizmetleri şu şekilde oluşturulur:

* İlk kez istenirler.
* Geliştirici tarafından doğrudan kapsayıcıya bir uygulama örneği sağlarken. Bu yaklaşım nadiren gereklidir.

Her sonraki istek aynı örneği kullanır. Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verin. Tek tasarım modelini uygulamayın ve tek bir atma kodu sağlayın. Hizmetler, kapsayıcıyı hizmetten çözümleyen kodla hiçbir şekilde atılmamalıdır. Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.

İle Singleton hizmetlerini kaydettirin <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> . Tek hizmetler iş parçacığı güvenli olmalıdır ve genellikle durum bilgisiz hizmetlerde kullanılır.

İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> . Uygulama kapatılıncaya kadar bellek yayımlanmadığı için, tek bir hizmetle bellek kullanımını göz önünde bulundurun.

> [!WARNING]
> Tek tek içinden kapsamlı bir hizmeti **Çözümleme * yapın** . Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir. Tek bir hizmeti kapsamlı veya geçici bir hizmetten çözümlemek çok iyidir.

## <a name="service-registration-methods"></a>Hizmet kayıt yöntemleri

Framework, belirli senaryolarda yararlı olan hizmet kayıt uzantısı yöntemleri sağlar:

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| Yöntem                                                                                                                                                                              | Automatic<br>object<br>elden | Birden çok<br>uygulamalar | Geçiş bağımsız değişkenleri |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | Evet                             | Evet                         | Hayır        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | Evet                             | Evet                         | Evet       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<MyDep>();`                                                                                                | Evet                             | Hayır                          | Hayır        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | Hayır                              | Evet                         | Evet       |
| `AddSingleton(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | Hayır                              | Hayır                          | Evet       |

Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın. [Test için izleme türleri](xref:test/integration-tests#inject-mock-services)olduğunda birden çok uygulama kullanılması yaygındır.

Hizmeti yalnızca bir uygulama türüyle kaydetmek, bu hizmeti aynı uygulama ve hizmet türüyle kaydetmeye eşdeğerdir. Bu, bir hizmetin birden çok uygulamasının açık bir hizmet türü kullanmayan yöntemler kullanılarak kaydedilamamasının nedenleridir. Bu yöntemler bir hizmetin birden çok _instances * kaydını yapabilir, ancak hepsi aynı *uygulama* türüne sahip olur.

Yukarıdaki hizmet kayıt yöntemlerinden herhangi biri aynı hizmet türünün birden çok hizmet örneğini kaydetmek için kullanılabilir. Aşağıdaki örnekte, `AddSingleton` hizmet türü olarak ile iki kez çağırılır `IMyDependency` . İçin ikinci çağrı, `AddSingleton` olarak çözümlendikten önceki bir öncekini geçersiz kılar `IMyDependency` ve aracılığıyla birden çok hizmet çözümlendiğinde bir öncekini ekler `IEnumerable<IMyDependency>` . Hizmetler, ile çözümlendiklerinde kaydedildikleri sırada görüntülenir `IEnumerable<{SERVICE}>` .

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

Framework Ayrıca, `TryAdd{LIFETIME}` yalnızca kayıtlı bir uygulama olmadığında hizmeti kaydeden genişletme yöntemleri de sağlar.

Aşağıdaki örnekte, `AddSingleton` `MyDependency` için bir uygulama olarak Yazmaçları çağrısı `IMyDependency` . ' A çağrı, `TryAddSingleton` `IMyDependency` zaten kayıtlı bir uygulamaya sahip olduğu için hiçbir etkiye sahip değildir.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

Daha fazla bilgi için bkz.

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) yöntemleri, yalnızca *aynı türde* bir uygulama olmadığında hizmeti kaydeder. Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` . Hizmetleri kaydederken, aynı türden biri zaten eklenmediyse geliştirici bir örnek eklememelidir. Genellikle, kitaplık yazarları `TryAddEnumerable` kapsayıcıda bir uygulamanın birden çok kopyasını kaydetmemek için kullanır.

Aşağıdaki örnekte, `TryAddEnumerable` `MyDependency` için bir uygulama olarak kaydeden ilk çağrı `IMyDependency1` . İçin ikinci çağrı kaydettirir `MyDependency` `IMyDependency2` . `IMyDependency1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü çağrının etkisi yoktur `MyDependency` :

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

Hizmet kaydı, aynı türde birden çok uygulama kaydedilirken genellikle sıralı olarak bağımsızdır.

`IServiceCollection` bir <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> nesne koleksiyonudur. Aşağıdaki örnek, oluşturma ve ekleme yoluyla bir hizmetin nasıl kaydedileceği gösterilmektedir `ServiceDescriptor` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

Yerleşik `Add{LIFETIME}` Yöntemler aynı yaklaşımı kullanır. Örneğin, bkz. [Addkapsamlıdır kaynak kodu](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).

### <a name="constructor-injection-behavior"></a>Oluşturucu Ekleme davranışı

Hizmetler, kullanılarak çözülebilir:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:
  * Kapsayıcıda kayıtlı olmayan nesneler oluşturur.
  * [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), MVC denetleyicileri ve [model ciltler](xref:mvc/models/model-binding)gibi Framework özellikleriyle kullanılır.

Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.

Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.

Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir. Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.

## <a name="entity-framework-contexts"></a>Entity Framework bağlamları

Varsayılan olarak, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan, Entity Framework bağlamları [kapsama yaşam süresi](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir. Farklı bir yaşam süresi kullanmak için, bir aşırı yükleme kullanarak yaşam süresini belirtin <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> . Belirli bir yaşam süresinin Hizmetleri, hizmetin yaşam süresinden kısa olan bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.

## <a name="lifetime-and-registration-options"></a>Ömür ve kayıt seçenekleri

Hizmet yaşam süreleri ve kayıt seçenekleri arasındaki farkı göstermek için, bir görevi tanımlayıcı ile bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` . Bir işlemin hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde hizmetin aynı ya da farklı örneklerini sağlar:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Aşağıdaki `Operation` sınıf önceki arabirimlerin tümünü uygular. `Operation`Oluşturucu BIR GUID oluşturur ve son 4 karakteri `OperationId` özellikte depolar:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

`Startup.ConfigureServices`Yöntemi, `Operation` adlandırılmış yaşam sürelerine göre sınıfının birden çok kaydı oluşturur:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

Örnek uygulama, isteklerin içindeki ve içindeki nesne yaşam sürelerini gösterir. `IndexModel`Ve ara yazılım her `IOperation` tür türü ister, `OperationId` her biri için günlük kaydı:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

Uygulamasına benzer şekilde, `IndexModel` Ara yazılım aynı hizmetleri çözümler:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

Kapsamındaki hizmetlerin yöntemde çözülmesi gerekir `InvokeAsync` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

Günlükçü çıktısı şunu gösterir:

* *Geçici* nesneler her zaman farklıdır. Geçici `OperationId` değer, `IndexModel` Ara yazılım içindeki ve içinde farklıdır.
* *Kapsamlı* nesneler her istek için aynıdır, ancak her istekte farklıdır.
* *Tek* nesneler her istek için aynıdır.

Günlüğe kaydetme çıkışını azaltmak için, dosyadaki *appsettings.Development.js* "Logging: LogLevel: Microsoft: Error" olarak ayarlayın:

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>Ana bilgisayardan Hizmetleri çağır

<xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) ile bir oluşturun. Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır.

Aşağıdaki örnek, kapsamındaki hizmete nasıl erişeceğinden `IMyDependency` ve yönteminin içinde nasıl çağrılacağını göstermektedir `WriteMessage` `Program.Main` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>Kapsam doğrulaması

Uygulama [geliştirme ortamında](xref:fundamentals/environments) çalıştırıldığında ve konağı oluşturmak Için [createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) çağırdığında, varsayılan hizmet sağlayıcısı şunları doğrulamak için denetimler gerçekleştirir:

* Kapsamlı hizmetler kök hizmet sağlayıcısından çözümlenmez.
* Kapsamlı hizmetler tekton 'a eklenmiş değildir.

Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> çağrıldığında oluşturulur. Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulamanın ömrüne karşılık gelir.

Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış. Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü, uygulama kapandığında yalnızca kök kapsayıcı tarafından atıldığı için etkin bir şekilde tek bir yükseltilir. Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .

Daha fazla bilgi için bkz. [kapsam doğrulaması](xref:fundamentals/host/web-host#scope-validation).

## <a name="request-services"></a>İstek Hizmetleri

Bir ASP.NET Core isteği içinde kullanılabilen hizmetler, [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur. Bir isteğin içinden hizmetler istendiğinde, hizmetler ve bağımlılıkları `RequestServices` koleksiyonundan çözümlenir.

Çerçeve, istek başına bir kapsam oluşturur ve `RequestServices` kapsamlı hizmet sağlayıcısını kullanıma sunar. Tüm kapsamlı hizmetler, istek etkin olduğu sürece geçerli olur.

> [!NOTE]
> Koleksiyondan Hizmetleri çözümlemek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` . Bu, test etmek daha kolay olan sınıfların sonucunu vermez.

## <a name="design-services-for-dependency-injection"></a>Bağımlılık ekleme için tasarım hizmetleri

Bağımlılık ekleme için Hizmetleri tasarlarken:

* Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının. Bunun yerine Singleton hizmetlerini kullanmak için uygulama tasarlayarak genel durum oluşturmamaya özen gösterin.
* Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin. Doğrudan örnekleme kodu belirli bir uygulamaya bağar.
* Hizmetleri küçük, iyi bir şekilde ve kolayca test edin.

Bir sınıfta çok fazla sayıda bağımlılık varsa, bu, sınıfta çok fazla sorumluluğun olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir işaret olabilir. Bazı sorumlulukları yeni sınıflara taşıyarak sınıfı yeniden düzenleme girişimi. RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın.

### <a name="disposal-of-services"></a>Hizmetlerin elden çıkarılması

Kapsayıcı, <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> oluşturduğu türleri çağırır. Kapsayıcıdan çözümlenen hizmetler hiçbir şekilde geliştirici tarafından atılamaz. Bir tür veya fabrika tek bir olarak kayıtlıysa, kapsayıcı kendiliğinden otomatik olarak atar.

Aşağıdaki örnekte, hizmetler hizmet kapsayıcısı tarafından oluşturulur ve otomatik olarak elden alınır:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

Hata ayıklama konsolu dizin sayfasının her yenilemesinde aşağıdaki çıktıyı gösterir:

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>Hizmet kapsayıcısı tarafından oluşturulmayan hizmetler

Aşağıdaki kodu inceleyin:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

Yukarıdaki kodda:

* Hizmet örnekleri hizmet kapsayıcısı tarafından oluşturulmaz.
* Framework Hizmetleri otomatik olarak atmaz.
* Geliştirici, hizmetleri elden atan sorumludur.

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Geçici ve paylaşılan örnekler için IDisposable Kılavuzu

#### <a name="transient-limited-lifetime"></a>Geçici, sınırlı ömür

**Senaryo**

Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:

* Örnek, kök kapsamda (kök kapsayıcı) çözümlenir.
* Kapsam bitmeden önce örnek atılmalıdır.

**Çözüm**

Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın. Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur. Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:

* Oluşturucusunda bir alın <xref:System.IServiceProvider> .
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcının bağımlılıkları için kapsayıcıyı kullanırken, örneği kapsayıcının dışında örneklemek için kullanın.

#### <a name="shared-instance-limited-lifetime"></a>Paylaşılan örnek, sınırlı ömür

**Senaryo**

Uygulamanın <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek olması gerekir, ancak <xref:System.IDisposable> örnek sınırlı bir yaşam süresine sahip olmalıdır.

**Çözüm**

Örneği kapsamlı bir ömür ile kaydedin. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . <xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın. Artık gerekli olmadığında kapsamı atın.

#### <a name="general-idisposable-guidelines"></a>Genel IDisposable yönergeleri

* <xref:System.IDisposable>Örnekleri geçici bir yaşam süresine kaydetmez. Bunun yerine fabrika modelini kullanın.
* <xref:System.IDisposable>Kök kapsamda geçici veya kapsamlı bir yaşam süresine sahip örnekleri çözümleyin. Bunun tek istisnası, uygulamanın oluşturma/yeniden oluşturma/yeniden oluşturma ve uygulama oluşturmasıdır <xref:System.IServiceProvider> , ancak bu ideal bir modeldir.
* Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> . <xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.
* Hizmetlerin ömrünü denetlemek için kapsamları kullanın. Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.

## <a name="default-service-container-replacement"></a>Varsayılan hizmet kapsayıcısı değiştirme

Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır. Desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:

* Özellik ekleme
* Ada göre ekleme
* Alt kapsayıcılar
* Özel ömür yönetimi
* `Func<T>` yavaş başlatma desteği
* Kural tabanlı kayıt

Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Drıioc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Yetkisiz kullanım](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Açık Inject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [E](https://jasperfx.github.io/lamar/)
* [Stakıbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a>İş parçacığı güvenliği

İş parçacığı güvenli Singleton Hizmetleri oluşturun. Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.

Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), iş parçacığı açısından güvenli olması gerekmez. Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından yalnızca bir kez çağrılması garanti edilir.

## <a name="recommendations"></a>Öneriler

* `async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez. C# zaman uyumsuz oluşturucuları desteklemediğinden, hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanın.
* Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının. Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir. Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır. Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının. DI aracılığıyla gerçek öğe istemek daha iyidir.
* Hizmetlere statik erişimi önleyin. Örneğin, başka bir yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik bir alan veya özellik olarak yakalanmaktan kaçının.
* Dı fabrikalarını hızlı ve zaman uyumlu tutun.
* *Hizmet bulucu deseninin* kullanmaktan kaçının. Örneğin, <xref:System.IServiceProvider.GetService%2A> yerine şunu kullandığınızda bir hizmet örneği elde etme çağrısı yapmayın:

  **Yanlış:**

    ![Yanlış kod](dependency-injection/_static/bad.png)

  **Doğru** :

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
* Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

<a name="ASP0000"></a>
* İçindeki çağrılardan <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> kaçının `ConfigureServices` . Çağırma `BuildServiceProvider` genellikle geliştirici içindeki bir hizmeti çözmek istediğinde oluşur `ConfigureServices` . Örneğin, yapılandırmasından yüklendiği durumu göz önünde bulundurun `LoginPath` . Aşağıdaki yaklaşımdan kaçının:

  ![BuildServiceProvider çağrılırken hatalı kod](~/fundamentals/dependency-injection/_static/badcodeX.png)

  Önceki görüntüde yeşil dalgalı çizgiyi seçtiğinizde `services.BuildServiceProvider` AŞAĞıDAKI ASP0000 uyarısı gösterilmektedir:

  > Uygulama kodundan ' BuildServiceProvider ' çağrısı ASP0000, oluşturulan Singleton Hizmetleri ek bir kopyasına neden olur. Bağımlılık ekleme hizmetleri gibi alternatifleri, ' configure ' için parametre olarak düşünün.

  Çağırma `BuildServiceProvider` ikinci bir kapsayıcı oluşturur ve bu, parçalanmış tek ton oluşturabilir ve birden çok kapsayıcı genelinde nesne grafiklerine başvuru oluşmasına neden olabilir.

  Almanın doğru yolu, `LoginPath` Seçenekler deseninin, dı için yerleşik desteğini kullanmaktır:

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* Atılabilir geçici hizmetler, aktiften çıkarma için kapsayıcı tarafından yakalanır. Bu, en üst düzey kapsayıcıdan çözümlenirse bir bellek sızıntısını açabilir.
* Uygulamanın kapsamlı hizmetleri yakalayan tekton içermediğinden emin olmak için kapsam doğrulamayı etkinleştirin. Daha fazla bilgi için bkz. [kapsam doğrulaması](#scope-validation).

Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz. Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.

Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* . Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>DI 'de çok kiracılı için önerilen desenler

[Orchard Core](https://github.com/OrchardCMS/OrchardCore) , ASP.NET Core üzerinde modüler, çok kiracılı uygulamalar oluşturmaya yönelik bir uygulama çerçevesidir. Daha fazla bilgi için bkz. [Orchard Core belgeleri](https://docs.orchardcore.net/en/dev/).

Yalnızca Orchard Core çerçevesini kullanarak, CMS 'ye özgü özelliklerden herhangi biri olmadan modüler ve çok kiracılı uygulamalar oluşturma örnekleri için bkz. [Orchard çekirdek örnekleri](https://github.com/OrchardCMS/OrchardCore.Samples) .

## <a name="framework-provided-services"></a>Framework tarafından sunulan hizmetler

`Startup.ConfigureServices`Yöntemi, uygulamanın kullandığı hizmetleri, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere kaydeder. Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir. ASP.NET Core şablonlarına dayalı uygulamalar için Framework, 250 'den fazla hizmeti kaydeder. 

Aşağıdaki tabloda, bu çerçeve kayıtlı hizmetlerinin küçük bir örneği listelenmektedir:

| Hizmet Türü                                                                                    | Ömür  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Larsa |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | Adet |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | Larsa |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | Adet |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | Larsa |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | Adet |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | Adet |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | Adet |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | Larsa |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | Adet |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | Adet |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | Adet |

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [Dı uygulaması geliştirme için NDC Konferansı desenleri](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Açık bağımlılıklar Ilkesi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)](https://www.martinfowler.com/articles/injection.html)
* [ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Steve Smith](https://ardalis.com/), [Scott Ade](https://scottaddie.com)ve [Brandon Dahler](https://github.com/brandondahler) tarafından

ASP.NET Core, sınıflar ve bunların bağımlılıkları arasında [denetimin INVERSION (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elde etmek için bir teknik olan bağımlılık ekleme (dı) yazılım tasarım modelini destekler.

MVC denetleyicileri içindeki bağımlılık eklenmesine özgü daha fazla bilgi için bkz <xref:mvc/controllers/dependency-injection> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Bağımlılık eklenmesine genel bakış

*Bağımlılık* , başka bir nesnenin gerektirdiği herhangi bir nesnedir. Aşağıdaki sınıfı, `MyDependency` `WriteMessage` bir uygulamadaki diğer sınıfların bağlı olduğu bir yöntemle inceleyin:

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

`MyDependency` `WriteMessage` Yöntemi bir sınıf için kullanılabilir hale getirmek için sınıfının bir örneği oluşturulabilir. `MyDependency`Sınıfı, sınıfının bir bağımlılığı olur `IndexModel` :

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

Sınıf oluşturur ve örneğe doğrudan bağlıdır `MyDependency` . Kod bağımlılıkları (önceki örnekte olduğu gibi) sorunlu olur ve aşağıdaki nedenlerden dolayı kaçınılması gerekir:

* `MyDependency`Farklı bir uygulamayla değiştirmek için, sınıfın değiştirilmesi gerekir.
* `MyDependency`Bağımlılıkları varsa, sınıfı tarafından yapılandırılması gerekir. Uygulamasına bağlı olarak, birden çok sınıfı olan büyük bir projede `MyDependency` yapılandırma kodu uygulama genelinde dağılmış hale gelir.
* Bu uygulamanın birim testi zordur. Uygulamanın `MyDependency` Bu yaklaşım ile mümkün olmayan bir sahte veya saplama sınıfı kullanması gerekir.

Bağımlılık ekleme bu sorunları şu şekilde giderir:

* Bağımlılık uygulamasını soyutlamak için bir arabirim veya temel sınıf kullanımı.
* Bir hizmet kapsayıcısına bağımlılığın kaydı. ASP.NET Core, yerleşik bir hizmet kapsayıcısı sağlar <xref:System.IServiceProvider> . Hizmetler, uygulamanın `Startup.ConfigureServices` yöntemine kaydedilir.
* Hizmetin kullanıldığı sınıf oluşturucusuna *ekleme* . Çerçeve, bağımlılığın bir örneğini oluşturma ve artık gerekli olmadığında bu uygulamayı atma sorumluluğunu alır.

[Örnek uygulamada](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), `IMyDependency` arabirim, hizmetin uygulamaya sağladığı bir yöntemi tanımlar:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Bu arabirim somut bir tür tarafından uygulanır, `MyDependency` :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`<xref:Microsoft.Extensions.Logging.ILogger`1>kendi oluşturucusunda bir ister. Bağımlılık ekleme işlemini zincirleme bir biçimde kullanmak olağan dışı değildir. Her istenen bağımlılık, kendi bağımlılıklarını ister. Kapsayıcı grafikteki bağımlılıkları çözer ve tamamen çözümlenen hizmeti döndürür. Çözümlenmesi gereken, genellikle *bağımlılık ağacı* , *bağımlılık grafiği* veya *nesne grafiği* olarak adlandırılan toplu bağımlılıklar kümesi.

`IMyDependency` ve `ILogger<TCategoryName>` hizmet kapsayıcısında kayıtlı olmalıdır. `IMyDependency` kaydedilir `Startup.ConfigureServices` . `ILogger<TCategoryName>` günlüğe kaydetme soyutlamaları altyapısı tarafından kaydedilir. bu nedenle, Framework tarafından varsayılan olarak kaydedilen [Framework tarafından sağlanmış bir hizmettir](#framework-provided-services) .

Kapsayıcı `ILogger<TCategoryName>` [(genel) açık türlerden](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)yararlanarak çözümlenir, her [(genel) oluşturulan türü](/dotnet/csharp/language-reference/language-specification/types#constructed-types)kaydetme ihtiyacını ortadan kaldırır:

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Örnek uygulamada, `IMyDependency` hizmet somut tür ile kaydedilir `MyDependency` . Kayıt, hizmet ömrünü tek bir isteğin kullanım ömrüne göre kapsamlar. [Hizmet yaşam süreleri](#service-lifetimes) bu konunun ilerleyen kısımlarında açıklanmıştır.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Her `services.Add{SERVICE_NAME}` uzantı yöntemi Hizmetleri ekler (ve potansiyel olarak yapılandırır). Örneğin, `services.AddMvc()` hizmet Razor SAYFALARıNı ve MVC gerektir öğesini ekler. Uygulamaların bu kuralı izlemesini öneririz. Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.

Hizmetin Oluşturucusu, gibi [yerleşik bir tür](/dotnet/csharp/language-reference/keywords/built-in-types-table)gerektiriyorsa, `string` tür [yapılandırma](xref:fundamentals/configuration/index) veya [Seçenekler düzeniyle](xref:fundamentals/configuration/options)eklenebilir:

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

Örnek uygulamada, `IMyDependency` örnek istenir ve hizmetin yöntemini çağırmak için kullanılır `WriteMessage` :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Başlangıca eklenen hizmetler

`Startup`Genel ana bilgisayar () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Hizmetler şu şekilde eklenebilir `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Framework tarafından sunulan hizmetler

`Startup.ConfigureServices`Yöntemi, Entity Framework Core ve ASP.NET Core MVC gibi platform özellikleri de dahil olmak üzere, uygulamanın kullandığı hizmetleri tanımlamaktan sorumludur. Başlangıçta, `IServiceCollection` için belirtilen, `ConfigureServices` [konağın nasıl yapılandırıldığına](xref:fundamentals/index#host)bağlı olarak Framework tarafından tanımlanan hizmetlere sahiptir. Çerçeve tarafından kaydedilmiş yüzlerce hizmete sahip olmak ASP.NET Core şablona dayalı bir uygulama için sık görülen bir durumdur. Aşağıdaki tabloda çerçeve kayıtlı hizmetlerden oluşan küçük bir örnek listelenmiştir.

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

Bir hizmet koleksiyonu genişletme yöntemi (ve gerekirse bağımlı hizmetleri) kaydetmek için kullanılabilir olduğunda, `Add{SERVICE_NAME}` Bu hizmet için gereken tüm hizmetleri kaydetmek üzere kural tek bir genişletme yöntemi kullanmaktır. Aşağıdaki kod, [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) uzantı yöntemlerini kullanarak kapsayıcıya ek hizmetler eklemenin bir örneğidir <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :

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

Daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> API belgelerindeki sınıfına bakın.

## <a name="service-lifetimes"></a>Hizmet yaşam süreleri

Kayıtlı her hizmet için uygun bir yaşam süresi seçin. ASP.NET Core hizmetler aşağıdaki yaşam süreleri ile yapılandırılabilir:

### <a name="transient"></a>Larsa

Geçici ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ), hizmet kapsayıcısından her istenilişinde oluşturulur. Bu ömür, hafif ve durumsuz hizmetler için en iyi şekilde kullanılır.

İstekleri işleyen uygulamalarda, geçici hizmetler isteğin sonuna atıldı.

### <a name="scoped"></a>Yayıl

Kapsamlı ömür Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ), istemci isteği başına bir kez oluşturulur (bağlantı).

İstekleri işleyen uygulamalarda, kapsamlı hizmetler isteğin sonuna atıldı.

> [!WARNING]
> Bir ara yazılım içinde kapsamlı bir hizmet kullanırken, hizmeti `Invoke` veya `InvokeAsync` yöntemine ekleyin. [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yoluyla ekleme, hizmeti tek bir gibi davranmaya zoryor. Daha fazla bilgi için bkz. <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Adet

Tek yaşam süresi Hizmetleri ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) her istendiğinde oluşturulur (veya çalıştırıldığında `Startup.ConfigureServices` ve hizmet kaydıyla bir örnek belirtildiğinde). Her sonraki istek aynı örneği kullanır. Uygulama tek davranış gerektiriyorsa, hizmet kapsayıcısının hizmetin ömrünü yönetmesine izin verilmesi önerilir. Tekil tasarım modelini uygulamayın ve nesnenin sınıfındaki ömrünü yönetmek için Kullanıcı kodu sağlayın.

İstekleri işleyen uygulamalarda, uygulama kapatılırken bırakıldığında tek hizmetler silinir <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> .

> [!WARNING]
> Kapsamlı bir hizmetin tek bir bilgisayardan çözümlenmesi tehlikelidir. Bu, sonraki istekleri işlerken hizmetin yanlış duruma gelmesine neden olabilir.

## <a name="service-registration-methods"></a>Hizmet kayıt yöntemleri

Hizmet kayıt uzantısı yöntemleri, belirli senaryolarda yararlı olan aşırı yüklemeler sunar.

| Yöntem | Automatic<br>object<br>elden | Birden çok<br>uygulamalar | Geçiş bağımsız değişkenleri |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<IMyDep, MyDep>();` | Evet | Evet | Hayır |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Evet | Evet | Evet |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Örnek:<br>`services.AddSingleton<MyDep>();` | Evet | Hayır | Hayır |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Hayır | Evet | Evet |
| `AddSingleton(new {IMPLEMENTATION})`<br>Örnekler:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Hayır | Hayır | Evet |

Tür çıkarma hakkında daha fazla bilgi için [Hizmetler 'In aktiften çıkarılması](#disposal-of-services) bölümüne bakın. Birden çok uygulama için yaygın bir senaryo, [test için bir sahte işlem türüdür](xref:test/integration-tests#inject-mock-services).

Hizmeti yalnızca bir uygulama türüyle kaydetmek, bu hizmeti aynı uygulama ve hizmet türüyle kaydetmeye eşdeğerdir. Bu, bir hizmetin birden çok uygulamasının açık bir hizmet türü kullanmayan yöntemler kullanılarak kaydedilamamasının nedenleridir. Bu yöntemler bir hizmetin birden fazla *örneğini* kaydedebilir, ancak hepsi aynı *uygulama* türüne sahip olur.

Yukarıdaki hizmet kayıt yöntemlerinden herhangi biri aynı hizmet türünün birden çok hizmet örneğini kaydetmek için kullanılabilir. Aşağıdaki örnekte, `AddSingleton` hizmet türü olarak ile iki kez çağırılır `IMyDependency` . İçin ikinci çağrı, `AddSingleton` olarak çözümlendikten önceki bir öncekini geçersiz kılar `IMyDependency` ve aracılığıyla birden çok hizmet çözümlendiğinde bir öncekini ekler `IEnumerable<IMyDependency>` . Hizmetler, ile çözümlendiklerinde kaydedildikleri sırada görüntülenir `IEnumerable<{SERVICE}>` .

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

Framework Ayrıca, `TryAdd{LIFETIME}` yalnızca kayıtlı bir uygulama olmadığında hizmeti kaydeden genişletme yöntemleri de sağlar.

Aşağıdaki örnekte, `AddSingleton` `MyDependency` için bir uygulama olarak Yazmaçları çağrısı `IMyDependency` . ' A çağrı, `TryAddSingleton` `IMyDependency` zaten kayıtlı bir uygulamaya sahip olduğu için hiçbir etkiye sahip değildir.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

Daha fazla bilgi için bkz.

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) yöntemleri yalnızca *aynı türde* bir uygulama yoksa hizmeti kaydeder. Aracılığıyla birden çok hizmet çözümlenir `IEnumerable<{SERVICE}>` . Hizmetleri kaydederken, geliştirici yalnızca aynı türden biri zaten eklenmediyse bir örnek eklemek istemektedir. Genellikle, bu yöntem, kapsayıcıda bir örneğin iki kopyasını kaydetmemek için kitaplık yazarları tarafından kullanılır.

Aşağıdaki örnekte, ilk satır `MyDep` için kaydedilir `IMyDep1` . İkinci satır için kaydedilir `MyDep` `IMyDep2` . `IMyDep1`Zaten kayıtlı bir uygulamasına sahip olduğundan, üçüncü satırın etkisi yoktur `MyDep` :

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
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Bağımlılık ekleme kapsayıcısında hizmet kaydı olmadan nesne oluşturulmasına izin verir. `ActivatorUtilities` Etiket Yardımcıları, MVC denetleyicileri ve model ciltler gibi kullanıcıya yönelik soyutlamalar ile kullanılır.

Oluşturucular bağımlılık ekleme tarafından sağlanmayan bağımsız değişkenleri kabul edebilir, ancak bağımsız değişkenlerin varsayılan değerleri ataması gerekir.

Hizmetler veya tarafından çözümlendiğinde `IServiceProvider` `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) *ortak* bir Oluşturucu gerektirir.

Hizmetler tarafından çözümlendiğinde `ActivatorUtilities` , [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) yalnızca bir adet geçerli oluşturucunun var olmasını gerektirir. Oluşturucu aşırı yüklemeleri desteklenir, ancak bağımsız değişkenleri bağımlılık ekleme tarafından yerine yalnızca bir aşırı yükleme bulunabilir.

## <a name="entity-framework-contexts"></a>Entity Framework bağlamları

Entity Framework bağlamlar genellikle, Web uygulaması veritabanı işlemleri normalde istemci isteği kapsamında olduğundan [kapsamlı ömür](#service-lifetimes) kullanılarak hizmet kapsayıcısına eklenir. Veritabanı bağlamı kaydedilirken bir [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) aşırı yüklemesiyle bir yaşam süresi belirtilmemişse, varsayılan yaşam süresi kapsamındadır. Belirli bir yaşam süresinin Hizmetleri, hizmetten daha kısa bir yaşam süresine sahip bir veritabanı bağlamı kullanmamalıdır.

## <a name="lifetime-and-registration-options"></a>Ömür ve kayıt seçenekleri

Ömür ve kayıt seçenekleri arasındaki farkı göstermek için, görevleri benzersiz bir tanımlayıcıya sahip bir işlem olarak temsil eden aşağıdaki arayüzleri göz önünde bulundurun `OperationId` . Bir işlem hizmetinin yaşam süresinin aşağıdaki arabirimler için nasıl yapılandırıldığına bağlı olarak kapsayıcı, bir sınıf tarafından istendiğinde aynı ya da farklı bir hizmet örneği sağlar:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Arabirimler `Operation` sınıfında uygulanır. `Operation`Bir tane sağlanmazsa Oluşturucu BIR GUID oluşturur:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

, `OperationService` Diğer türlerin her birine bağlı olarak kaydedilir `Operation` . `OperationService`Bağımlılık ekleme yoluyla istendiğinde, her bir hizmetin yeni bir örneğini ya da bağımlı hizmetin kullanım ömrü temelinde mevcut bir örneği alır.

* Kapsayıcıda istendiğinde geçici hizmetler oluşturulduğunda, `OperationId` `IOperationTransient` hizmet öğesinden farklı olur `OperationId` `OperationService` . `OperationService` sınıfının yeni bir örneğini alır `IOperationTransient` . Yeni örnek farklı bir şekilde oluşturur `OperationId` .
* İstemci isteği başına kapsamlı hizmetler oluşturulduğunda, `OperationId` `IOperationScoped` hizmetin istemci isteği içindeki ile aynı olması gerekir `OperationService` . İstemci istekleri arasında her iki hizmet de farklı bir `OperationId` değer paylaşır.
* Tek ve tek örnekli hizmetler bir kez oluşturulduğunda ve tüm istemci isteklerinde ve tüm hizmetlerde kullanıldığında, `OperationId` tüm hizmet istekleri genelinde sabittir.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

' De `Startup.ConfigureServices` , her tür kapsayıcısına, adlandırılmış ömrüne göre eklenir:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance`Hizmet, BILINEN kimliği olan belirli bir örnek kullanıyor `Guid.Empty` . Bu tür kullanımda olduğunda (GUID 'sinin tümü sıfırlardan tamamen) Bu bir şey vardır.

Örnek uygulama, bireysel istekler içindeki ve içindeki nesne yaşam sürelerini gösterir. Örnek uygulama, `IndexModel` her `IOperation` tür ve ' i ister `OperationService` . Daha sonra sayfa, tüm sayfa modeli sınıfının ve hizmetin `OperationId` değerlerini özellik atamaları aracılığıyla görüntüler:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Aşağıdaki iki çıktıda iki isteğin sonuçları gösterilmektedir:

**İlk istek:**

Denetleyici işlemleri:

Geçici: d233e165-f417-469B-a866-1cf1935d2518  
Kapsam: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationService` operasyonları

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

`OperationService` operasyonları

Geçici: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Kapsam: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Tek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Örnek: 00000000-0000-0000-0000-000000000000

`OperationId`Değerlerin bir istek içinde ve istekler arasında değiştiğini gözlemleyin:

* *Geçici* nesneler her zaman farklıdır. Hem `OperationId` Birinci hem de ikinci istemci isteklerinin geçici değeri hem işlemler hem de `OperationService` istemci istekleri arasında farklıdır. Her hizmet isteğine ve istemci isteğine yeni bir örnek sağlanır.
* *Kapsamlı* nesneler istemci isteği içinde aynıdır ancak istemci istekleri arasında farklıdır.
* *Tek* nesneler, `Operation` ' de bir örneğin sağlanmadığına bakılmaksızın her nesne için ve her istek için aynıdır `Startup.ConfigureServices` .

## <a name="call-services-from-main"></a>Ana bilgisayardan Hizmetleri çağır

<xref:Microsoft.Extensions.DependencyInjection.IServiceScope>Uygulamanın kapsamındaki bir kapsamlı hizmeti çözümlemek Için [ıvicescopefactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) ile bir oluşturun. Bu yaklaşım, başlatma görevlerini çalıştırmak üzere başlangıçta kapsamlı bir hizmete erişmek için yararlıdır. Aşağıdaki örnek, içinde için nasıl bağlam alınacağını gösterir `MyScopedService` `Program.Main` :

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

Kök hizmet sağlayıcısı <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> çağrıldığında oluşturulur. Kök hizmet sağlayıcısının ömrü, sağlayıcının uygulamayla başladığı ve uygulama kapandığında bırakıldığı uygulama/sunucunun yaşam süresine karşılık gelir.

Kapsamlı hizmetler kendilerini oluşturan kapsayıcı tarafından atılmış. Kök kapsayıcıda kapsamlı bir hizmet oluşturulduysa, hizmetin ömrü etkin şekilde tek başına yükseltilir çünkü yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından atılmış olur. Hizmet kapsamlarını doğrulamak, çağrıldığında bu durumları yakalar `BuildServiceProvider` .

Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#scope-validation>.   

## <a name="request-services"></a>İstek Hizmetleri

ASP.NET Core isteği içinde bulunan hizmetler, `HttpContext` [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) koleksiyonu aracılığıyla sunulur.

İstek Hizmetleri, uygulamanın bir parçası olarak yapılandırılan ve istenen hizmetleri temsil eder. Nesneler bağımlılıklar belirttiğinizde, bunlar ' de bulunan türler tarafından karşılanır `RequestServices` `ApplicationServices` .

Genellikle, uygulamanın bu özellikleri doğrudan kullanmamalıdır. Bunun yerine, sınıfların Sınıf oluşturucuları aracılığıyla gerektirdiği türleri isteyin ve çerçevenin bağımlılıkları eklemesine izin verin. Bu, test etmek daha kolay olan sınıfları oluşturur.

> [!NOTE]
> Koleksiyona erişmek için Oluşturucu parametreleri olarak bağımlılıklar istemeyi tercih edin `RequestServices` .

## <a name="design-services-for-dependency-injection"></a>Bağımlılık ekleme için tasarım hizmetleri

En iyi uygulamalar şunlardır:

* Bağımlılıklarını almak için bağımlılık ekleme 'yi kullanmak üzere Hizmetleri tasarlayın.
* Durum bilgisi olan statik sınıflar ve Üyeler kullanmaktan kaçının. Genel durum oluşturulmasını önlemek yerine, tek tek Hizmetleri kullanmak için uygulamaları tasarlayın.
* Hizmetler içindeki bağımlı sınıfların doğrudan örneklenmesini önleyin. Doğrudan örnekleme kodu belirli bir uygulamaya bağar.
* Uygulama sınıflarını küçük, iyi bir şekilde ve kolayca test edin.

Bir sınıfta çok fazla sayıda bağımlılık varsa, bu genellikle sınıfta çok fazla sorumluluk olduğu ve [tek sorumluluk ilkesini (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)ihlal eden bir imzadır. Bazı sorumlulukları yeni bir sınıfa taşıyarak sınıfı yeniden düzenleme girişimi. RazorSayfaların sayfa modeli sınıflarının ve MVC denetleyici sınıflarının Kullanıcı arabirimi endişeleri üzerine odaklandığını unutmayın. İş kuralları ve veri erişimi uygulama ayrıntıları, bu [ayrı kaygılara](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)uygun sınıflarda tutulmalıdır.

### <a name="disposal-of-services"></a>Hizmetlerin elden çıkarılması

Kapsayıcı, <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> oluşturduğu türleri çağırır. Kapsayıcıda Kullanıcı kodu tarafından bir örnek eklenirse, otomatik olarak atılamaz.

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

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Geçici ve paylaşılan örnekler için IDisposable Kılavuzu

#### <a name="transient-limited-lifetime"></a>Geçici, sınırlı ömür

**Senaryo**

Uygulama, <xref:System.IDisposable> aşağıdaki senaryolardan biri için geçici bir yaşam süresine sahip bir örnek gerektirir:

* Örnek, kök kapsamda çözümlenir.
* Kapsam bitmeden önce örnek atılmalıdır.

**Çözüm**

Üst kapsamın dışında bir örnek oluşturmak için fabrika modelini kullanın. Bu durumda, uygulama genellikle `Create` son türün oluşturucusunu doğrudan çağıran bir yönteme sahip olur. Son türün başka bağımlılıkları varsa, fabrika şunları yapabilir:

* Oluşturucusunda bir alın <xref:System.IServiceProvider> .
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>Kapsayıcısını, onun bağımlılıkları için kullanırken, kapsayıcının dışında örneğini oluşturmak için kullanın.

#### <a name="shared-instance-limited-lifetime"></a>Paylaşılan örnek, sınırlı ömür

**Senaryo**

Uygulama, <xref:System.IDisposable> birden çok hizmet arasında paylaşılan bir örnek gerektirir, ancak <xref:System.IDisposable> sınırlı bir yaşam süresine sahip olmalıdır.

**Çözüm**

Örneği kapsamlı bir ömür ile kaydedin. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>Başlatmak ve yeni bir oluşturmak için kullanın <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . <xref:System.IServiceProvider>Gerekli hizmetleri almak için kapsam ' i kullanın. Yaşam süresi sona erdirmek için kapsamı atın.

#### <a name="general-guidelines"></a>Genel Yönergeler

* <xref:System.IDisposable>Örnekleri geçici bir kapsamla kaydetme. Bunun yerine fabrika modelini kullanın.
* Kök kapsamdaki geçici veya kapsamlı <xref:System.IDisposable> örnekleri çözün. Tek genel özel durum, uygulamanın, ideal bir model olmayan, oluşturup yeniden oluşturduğu ve <xref:System.IServiceProvider> kaldırdıkları durumdur.
* Bir <xref:System.IDisposable> bağımlılık alma yoluyla, alıcının kendisini uygulaması gerekmez <xref:System.IDisposable> . <xref:System.IDisposable>Bağımlılığın alıcısı <xref:System.IDisposable.Dispose%2A> Bu bağımlılıkta çağrımamalıdır.
* Kapsamların hizmet ömrünü denetlemek için kullanılması gerekir. Kapsamlar hiyerarşik değildir ve kapsamlar arasında özel bir bağlantı yoktur.

## <a name="default-service-container-replacement"></a>Varsayılan hizmet kapsayıcısı değiştirme

Yerleşik hizmet kapsayıcısı, çerçeve ihtiyaçlarına ve çoğu tüketici uygulamasına hizmet vermek için tasarlanmıştır. Yerleşik kapsayıcının desteklemediği belirli bir özelliğe ihtiyacınız yoksa, yerleşik kapsayıcının kullanılması önerilir, örneğin:

* Özellik ekleme
* Ada göre ekleme
* Alt kapsayıcılar
* Özel ömür yönetimi
* `Func<T>` yavaş başlatma desteği
* Kural tabanlı kayıt

Aşağıdaki üçüncü taraf kapsayıcıları ASP.NET Core uygulamalarla kullanılabilir:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Drıioc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Yetkisiz kullanım](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Açık Inject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [E](https://jasperfx.github.io/lamar/)
* [Stakıbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>İş parçacığı güvenliği

İş parçacığı güvenli Singleton Hizmetleri oluşturun. Tek bir hizmetin geçici bir hizmete bağımlılığı varsa, geçici hizmet aynı zamanda tek tek tarafından nasıl kullanıldığına bağlı olarak iş parçacığı güvenliği de gerektirebilir.

Tek bir hizmetin fabrika yöntemi (örneğin, AddSingleton için ikinci bağımsız değişken [ \<TService> \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), iş parçacığı açısından güvenli olması gerekmez. Bir Type ( `static` ) Oluşturucusu gibi, tek bir iş parçacığı tarafından bir kez çağrılması garanti edilir.

## <a name="recommendations"></a>Öneriler

* `async/await` ve `Task` tabanlı hizmet çözümlemesi desteklenmez. C# zaman uyumsuz oluşturucuları desteklemez; Bu nedenle, önerilen model hizmeti zaman uyumlu olarak çözümledikten sonra zaman uyumsuz yöntemler kullanmaktır.

* Veri ve yapılandırmayı doğrudan hizmet kapsayıcısında saklamaktan kaçının. Örneğin, bir kullanıcının alışveriş sepeti genellikle hizmet kapsayıcısına eklenmemelidir. Yapılandırma, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmalıdır. Benzer şekilde, yalnızca başka bir nesneye erişime izin vermek için mevcut olan "veri sahibi" nesnelerinden kaçının. DI aracılığıyla gerçek öğe istemek daha iyidir.
* Hizmetlere statik erişimi önleyin. Örneğin, bir başka yerde kullanmak üzere [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) statik olarak yazılanmaktan kaçının.

* [Denetim](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) stratejilerini kapsayan *hizmet bulucu deseninin* kullanmaktan kaçının.
  * <xref:System.IServiceProvider.GetService*>Bunun yerine, şunu kullandığınızda bir hizmet örneği elde etmek için çağırmayın:

    **Yanlış:**

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    **Doğru** :

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

* Kullanarak çalışma zamanında bağımlılıkları çözümleyen bir fabrikaya ekleme önleyin <xref:System.IServiceProvider.GetService*> .
* Uygulamasına statik erişimi önleyin `HttpContext` (örneğin, [ıhttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Tüm öneri kümeleri gibi, bir öneriyi yok saymayı yok saymış durumlarla karşılaşabilirsiniz. Özel durumlar nadiren, genellikle Framework içindeki özel durumlardır.

Dı, statik/genel nesne erişim desenlerinin bir *alternatifidir* . Statik nesne erişimi ile karıştırırsanız, dı 'nin avantajlarını fark edemeyebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [ASP.NET Core ' de ıdisposa, ' i atılanmanın dört yolu](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Bağımlılık ekleme (MSDN) ile ASP.NET Core temizleme kodu yazma](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Açık bağımlılıklar Ilkesi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Denetim kapsayıcıları ve bağımlılık ekleme deseninin Inversion 'ı (Marwler)](https://www.martinfowler.com/articles/injection.html)
* [ASP.NET Core DI 'de birden çok arabirime sahip bir hizmeti kaydetme](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
