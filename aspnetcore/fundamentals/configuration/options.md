---
title: ASP.NET Çekirdekteki seçenekler deseni
author: rick-anderson
description: ASP.NET Core uygulamalarında ilgili ayar gruplarını temsil etmek için seçenekler deseninin nasıl kullanılacağını keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665461"
---
# <a name="options-pattern-in-aspnet-core"></a>ASP.NET Çekirdekteki seçenekler deseni

::: moniker range=">= aspnetcore-3.0"

Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara yalıtıldığında, uygulama iki önemli yazılım mühendisliği ilkesine bağlıdır:

* Yapılandırma ayarlarına bağlı olan [Arabirim Ayırma İlkesi (ISS) veya Kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Senaryoları (sınıflar), yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* [Uygulamanın](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; farklı bölümleri için Endişelerin Ayrılması Ayarları bağımlı değildir veya birbirine bağlı değildir.

Seçenekler, yapılandırma verilerini doğrulamak için bir mekanizma da sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="package"></a>Paket

[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketi ASP.NET Core uygulamalarında örtülü olarak başvurulabilir.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>seçenekleri almak ve örneğin seçenek bildirimlerini yönetmek için `TOptions` kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Bildirimleri değiştirme
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçici seçenekler geçersiz<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>liği ( )

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenekler örneklerinden sorumludur. Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir yöntemi var. Varsayılan uygulama tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kayıtlı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> alır ve tüm yapılandırmaları ilk olarak çalıştırın, ardından yapılandırma sonrası. Bu ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> sadece <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uygun arabirimi çağırır ayırt eder.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>`TOptions` örnekleri <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> önbelleğe almak için kullanılır. Değerin <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> yeniden hesaplanması için monitördeki seçenekler örneklerini geçersiz kılır (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler ile el ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>tanıtılabilir. Yöntem, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> adlandırılmış tüm örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda yararlıdır. Daha fazla bilgi için [IOptionsSnapshot bölümüyle yapılandırma verilerini yeniden yükle](#reload-configuration-data-with-ioptionssnapshot) bölümüne bakın.

<xref:Microsoft.Extensions.Options.IOptions%601>seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryoları <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>desteklemez. Arabirimi zaten <xref:Microsoft.Extensions.Options.IOptions%601> kullanan ve sağladığı <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>senaryoları gerektirmeyen varolan çerçeveleri ve kitaplıkları kullanmaya devam edebilirsiniz. <xref:Microsoft.Extensions.Options.IOptions%601>

## <a name="general-options-configuration"></a>Genel seçenekler yapılandırması

Genel seçenekler yapılandırması örnek uygulamada Örnek 1 olarak gösterilmiştir.

Bir seçenek sınıfı, ortak parametresiz bir oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıf, `MyOptions`iki özelliği `Option1` vardır `Option2`ve . Varsayılan değerleri ayarlamak isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf `Option1`oluşturucusu varsayılan değerini ayarlar. `Option2`özelliği doğrudan başharfe alarak belirlenen varsayılan bir değere sahiptir (*Modeller/MyOptions.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Sınıf, `MyOptions` yapılandırmaya <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> bağlı ve hizmet kapsayıcısına eklenir:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ayarlara erişmek için [constructor bağımlılık enjeksiyonu](xref:mvc/controllers/dependency-injection) kullanır (*Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appsettings.json* dosyası için `option1` değerleri belirtir `option2`ve:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Ayarlar dosyasından <xref:System.Configuration.ConfigurationBuilder> seçenekler yapılandırmasını yüklemek için özel bir madde kullanırken, temel yolun doğru ayarlandığından onaylayın:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Ayarlar dosyasından seçenekler yapılandırmasını ayarlarken temel yolu açıkça <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlamak gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilci ile basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada Örnek 2 olarak gösterilmiştir.

Seçenekler değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama `MyOptionsWithDelegateConfig` sınıfı kullanır ( Modeller */MyOptionsWithDelegeConfig.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına ikinci bir hizmet eklenir. Bu ile bağlama yapılandırmak için `MyOptionsWithDelegateConfig`bir temsilci kullanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinden edinilebilir ve kayıtlı oldukları sırada uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her arama <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler. Önceki örnekte, değerleri `Option1` ve `Option2` her ikisi de *appsettings.json*belirtilir, `Option1` `Option2` ancak değerleri ve yapılandırılan temsilci tarafından geçersiz kılındı.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, belirtilen son yapılandırma kaynağı *kazanır* ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenek yapılandırması örnek uygulamada Örnek 3 olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarıyla (sınıflar) ilgili seçenekler sınıfları oluşturmalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümleri yalnızca kullandıkları yapılandırma değerlerine erişebilir.

Seçenekleri yapılandırmaya bağlarken, seçenekler türündeki her özellik formun `property[:sub-property:]`yapılandırma anahtarına bağlıdır. Örneğin, `MyOptions.Option1` özellik `Option1` *appsettings.json*özelliğinden `option1` okunan anahtara bağlıdır.

Aşağıdaki kodda, hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına üçüncü bir hizmet eklenir. *Appsettings.json* dosyasının `MySubOptions` bölümüne `subsection` bağlanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Yöntem `GetSection` <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerektirir.

Örneğin *appsettings.json* dosyası için `subsection` `suboption1` anahtarları olan bir `suboption2`üye tanımlar ve:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

Sınıf `MySubOptions` özellikleri tanımlar `SubOption1` ve `SubOption2`, seçenekleri değerlerini tutmak için *(Modeller / MySubOptions.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` yöntemi seçenekleri değerleri *(Pages/Index.cshtml.cs)* ile bir dize döndürür:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, `OnGet` yöntem alt seçenek sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Seçenekler enjeksiyon

Seçenekler enjeksiyon örnek uygulamada Örnek 4 olarak gösterilmiştir.

Enjekte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> edin:

* Razor direktifi [`@inject`](xref:mvc/views/razor#inject) ile bir Razor sayfası veya MVC görünümü.
* Bir sayfa veya görünüm modeli.

Örnek uygulamadan aşağıdaki örnek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline enjekte edilir (*Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl enjekte edilebildiğini gösterir:

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenekler değerleri işlenen sayfada gösterilir:

![Opsiyon değerleri Option1: value1_from_json ve Option2: -1 modelden ve görünüme enjeksiyon ile yüklenir.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>IOptionsSnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yüklemek örnek uygulamada Örnek 5'te gösterilmiştir.

Kullanma, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçenekler istek kullanım ömrü boyunca erişildiğinde ve önbelleğe aldığında istek başına bir kez hesaplanır.

Arasındaki `IOptionsMonitor` fark `IOptionsSnapshot` ve şudur:

* `IOptionsMonitor`geçerli seçenek değerlerini herhangi bir zamanda alan ve özellikle singleton bağımlılıklarında kullanışlı olan [tek tonluk](xref:fundamentals/dependency-injection#singleton) bir hizmettir.
* `IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduruldýığı sınırdaki seçeneklerin anlık görüntüsünü saÄ `IOptionsSnapshot<T>` lıyor. Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklar ile kullanılmak üzere tasarlanmıştır.

Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* değişikliklerden sonra yeni bir yeninin nasıl oluşturulduğunu gösterir (*Pages/Index.cshtml.cs).* Sunucuya gelen birden çok istek, dosya değiştirilene ve yapılandırma yeniden yüklenene kadar *appsettings.json* dosyası tarafından sağlanan sabit değerleri döndürür.

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki resim, `option1` *appsettings.json* dosyasından yüklenen başlangıç ve `option2` değerleri gösterir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*appsettings.json* dosyasındaki değerleri `value1_from_json UPDATED` değiştirin `200`ve . *appsettings.json* dosyasını kaydedin. Seçeneklerin güncelleştirdiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IConfigureNamedOptions ile adlandırılmış seçenekler desteği

Adlandırılmış seçenekler <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> desteği örnek uygulamada Örnek 6 olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenekler yapılandırmaları arasında ayrım yapmasına olanak tanır. Örnek uygulamada, adlandırılmış [seçenekler,](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) [ConfigureNamedOptions\<TOptions> çağıran OptionsServiceCollectionExtensions.Configure ile bildirilir. Uzatma yöntemini yapılandırın.](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) Adlandırılmış seçenekler büyük/küçük harf duyarlıdır.

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama ( <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> *Pages/Index.cshtml.cs)* ile adlandırılmış seçeneklere erişir:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırılırken, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`değerler, *appsettings.json* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2`değerler tarafından sağlanır:

* Delege `named_options_2` `ConfigureServices` için `Option1`.
* Sınıf tarafından `Option2` sağlanan varsayılan değer. `MyOptions`

## <a name="configure-all-options-with-the-configureall-method"></a>YapılandırmaTüm yöntemiyle tüm seçenekleri yapılandır

Tüm seçenekler örneklerini yöntemle yapılandırın. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örnekleri için yapılandırır. `Startup.ConfigureServices` Yönteme el ile aşağıdaki kodu ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu üretir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Varolan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> `Options.DefaultName` örnekler, örneği hedefleyen olarak kabul `string.Empty`edilir. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>ayrıca <xref:Microsoft.Extensions.Options.IConfigureOptions%601>uygular. Varsayılan uygulama her <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun kullanmak için mantık vardır. `null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek (ve<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanmak) için kullanılır.

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>`TOptions` örnekleri yapılandırmak için kullanılır. `OptionsBuilder`sonraki tüm aramalarda görünmek yerine ilk `AddOptions<TOptions>(string optionsName)` çağrıya yalnızca tek bir parametre olarak adlandırılmış seçenekleri oluşturmayı kolaylaştırır. Seçenekler doğrulaması `ConfigureOptions` ve hizmet bağımlılıklarını kabul eden `OptionsBuilder`aşırı yüklemeler yalnızca .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Seçenekleri yapılandırmak için DI hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken bağımlılık enjeksiyonundan diğer hizmetlere erişebilirsiniz:

* [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) [üzerinde Yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. `OptionsBuilder<TOptions>`seçenekleri yapılandırmak için en fazla beş hizmetkullanımına izin [veren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yapılandırma aşırı yükleri sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünüzü oluşturun ve türü hizmet olarak kaydedin.

Bir hizmet oluşturmak daha karmaşık olduğundan, [Yapılandırma'ya](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)bir yapılandırma temsilcisi geçmenizi öneririz. Kendi türünüzü oluşturmak, [Yapıl'ı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)kullandığınızda çerçevenin sizin için yaptığıyla eşdeğerdir. [Yapıyı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) Çağırma, belirtilen genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>hizmet türlerini kabul eden bir oluşturucuya sahip geçici bir genel kayıt kaydeder. 

## <a name="options-validation"></a>Seçenekler doğrulama

Seçenekler doğrulaması, seçenekler yapılandırıldığınızda seçenekleri doğrulamanızı sağlar. Seçenekler `Validate` geçerliyse ve `false` geçerli `true` değilse döndüren bir doğrulama yöntemiyle arama:

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Önceki örnek, adlandırılmış seçenekler `optionalOptionsName`örneğini . Varsayılan seçenekler örneği. `Options.DefaultName`

Doğrulama, seçenekler örneği oluşturulduğunda çalışır. Bir seçenek örneğinin, ilk erişime de doğrulamayı geçirmesi garanti edilir.

> [!IMPORTANT]
> Seçenekler doğrulaması, seçenekler örneği oluşturulduktan sonra seçenekler intibaklarına karşı koruma zedler. Örneğin, `IOptionsSnapshot` seçenekler ilk erişildiğinde istek başına bir kez oluşturulur ve doğrulanır. Seçenekler, `IOptionsSnapshot` aynı istek için sonraki erişim *denemelerinde*yeniden doğrulanmaz.

Yöntem `Validate` bir `Func<TOptions, bool>`. Doğrulamayı tam olarak özelleştirmek için aşağıdakileri uygulayın: `IValidateOptions<TOptions>`

* Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Başka bir seçenek türüne bağlı doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`Doğrulama:

* Belirli bir adlandırılmış seçenekler örneği.
* Tüm seçenekler `name` `null`ne zaman .

Arabirimi `ValidateOptionsResult` uygulamanızdan bir döndürme:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Veri Ek Açıklama tabanlı doğrulama [microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> yöntemi arayarak `OptionsBuilder<TOptions>`kullanılabilir. `Microsoft.Extensions.Options.DataAnnotations`ASP.NET Core uygulamalarında dolaylı olarak başvurulmusur.

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

Istekli doğrulama (başlangıçta hızlı başarısız) gelecekteki bir sürüm için düşünülmektedir.

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası'nı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>' ile ayarlama Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra yapılandırma sonrası çalışır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>adlı seçenekleri yapılandırmak için kullanılabilir:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlatma sırasında seçeneklere erişim

<xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> hizmetler `Configure` yöntem `Startup.Configure`yürütülmeden önce inşa olduğundan, kullanılabilir.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Kullanmayın <xref:Microsoft.Extensions.Options.IOptions%601> ya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> da `Startup.ConfigureServices`içinde. Hizmet kayıtlarının sıralanması nedeniyle tutarsız bir seçenek durumu oluşabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara yalıtıldığında, uygulama iki önemli yazılım mühendisliği ilkesine bağlıdır:

* Yapılandırma ayarlarına bağlı olan [Arabirim Ayırma İlkesi (ISS) veya Kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Senaryoları (sınıflar), yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* [Uygulamanın](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; farklı bölümleri için Endişelerin Ayrılması Ayarları bağımlı değildir veya birbirine bağlı değildir.

Seçenekler, yapılandırma verilerini doğrulamak için bir mekanizma da sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

[Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun veya [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>seçenekleri almak ve örneğin seçenek bildirimlerini yönetmek için `TOptions` kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Bildirimleri değiştirme
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçici seçenekler geçersiz<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>liği ( )

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenekler örneklerinden sorumludur. Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir yöntemi var. Varsayılan uygulama tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kayıtlı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> alır ve tüm yapılandırmaları ilk olarak çalıştırın, ardından yapılandırma sonrası. Bu ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> sadece <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uygun arabirimi çağırır ayırt eder.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>`TOptions` örnekleri <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> önbelleğe almak için kullanılır. Değerin <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> yeniden hesaplanması için monitördeki seçenekler örneklerini geçersiz kılır (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler ile el ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>tanıtılabilir. Yöntem, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> adlandırılmış tüm örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda yararlıdır. Daha fazla bilgi için [IOptionsSnapshot bölümüyle yapılandırma verilerini yeniden yükle](#reload-configuration-data-with-ioptionssnapshot) bölümüne bakın.

<xref:Microsoft.Extensions.Options.IOptions%601>seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryoları <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>desteklemez. Arabirimi zaten <xref:Microsoft.Extensions.Options.IOptions%601> kullanan ve sağladığı <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>senaryoları gerektirmeyen varolan çerçeveleri ve kitaplıkları kullanmaya devam edebilirsiniz. <xref:Microsoft.Extensions.Options.IOptions%601>

## <a name="general-options-configuration"></a>Genel seçenekler yapılandırması

Genel seçenekler yapılandırması örnek uygulamada Örnek 1 olarak gösterilmiştir.

Bir seçenek sınıfı, ortak parametresiz bir oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıf, `MyOptions`iki özelliği `Option1` vardır `Option2`ve . Varsayılan değerleri ayarlamak isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf `Option1`oluşturucusu varsayılan değerini ayarlar. `Option2`özelliği doğrudan başharfe alarak belirlenen varsayılan bir değere sahiptir (*Modeller/MyOptions.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Sınıf, `MyOptions` yapılandırmaya <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> bağlı ve hizmet kapsayıcısına eklenir:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ayarlara erişmek için [constructor bağımlılık enjeksiyonu](xref:mvc/controllers/dependency-injection) kullanır (*Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appsettings.json* dosyası için `option1` değerleri belirtir `option2`ve:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Ayarlar dosyasından <xref:System.Configuration.ConfigurationBuilder> seçenekler yapılandırmasını yüklemek için özel bir madde kullanırken, temel yolun doğru ayarlandığından onaylayın:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Ayarlar dosyasından seçenekler yapılandırmasını ayarlarken temel yolu açıkça <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlamak gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilci ile basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada Örnek 2 olarak gösterilmiştir.

Seçenekler değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama `MyOptionsWithDelegateConfig` sınıfı kullanır ( Modeller */MyOptionsWithDelegeConfig.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına ikinci bir hizmet eklenir. Bu ile bağlama yapılandırmak için `MyOptionsWithDelegateConfig`bir temsilci kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinden edinilebilir ve kayıtlı oldukları sırada uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her arama <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler. Önceki örnekte, değerleri `Option1` ve `Option2` her ikisi de *appsettings.json*belirtilir, `Option1` `Option2` ancak değerleri ve yapılandırılan temsilci tarafından geçersiz kılındı.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, belirtilen son yapılandırma kaynağı *kazanır* ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenek yapılandırması örnek uygulamada Örnek 3 olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarıyla (sınıflar) ilgili seçenekler sınıfları oluşturmalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümleri yalnızca kullandıkları yapılandırma değerlerine erişebilir.

Seçenekleri yapılandırmaya bağlarken, seçenekler türündeki her özellik formun `property[:sub-property:]`yapılandırma anahtarına bağlıdır. Örneğin, `MyOptions.Option1` özellik `Option1` *appsettings.json*özelliğinden `option1` okunan anahtara bağlıdır.

Aşağıdaki kodda, hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına üçüncü bir hizmet eklenir. *Appsettings.json* dosyasının `MySubOptions` bölümüne `subsection` bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Yöntem `GetSection` <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerektirir.

Örneğin *appsettings.json* dosyası için `subsection` `suboption1` anahtarları olan bir `suboption2`üye tanımlar ve:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Sınıf `MySubOptions` özellikleri tanımlar `SubOption1` ve `SubOption2`, seçenekleri değerlerini tutmak için *(Modeller / MySubOptions.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` yöntemi seçenekleri değerleri *(Pages/Index.cshtml.cs)* ile bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, `OnGet` yöntem alt seçenek sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Seçenekler enjeksiyon

Seçenekler enjeksiyon örnek uygulamada Örnek 4 olarak gösterilmiştir.

Enjekte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> edin:

* Razor direktifi [`@inject`](xref:mvc/views/razor#inject) ile bir Razor sayfası veya MVC görünümü.
* Bir sayfa veya görünüm modeli.

Örnek uygulamadan aşağıdaki örnek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline enjekte edilir (*Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl enjekte edilebildiğini gösterir:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenekler değerleri işlenen sayfada gösterilir:

![Opsiyon değerleri Option1: value1_from_json ve Option2: -1 modelden ve görünüme enjeksiyon ile yüklenir.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>IOptionsSnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yüklemek örnek uygulamada Örnek 5'te gösterilmiştir.

Kullanma, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçenekler istek kullanım ömrü boyunca erişildiğinde ve önbelleğe aldığında istek başına bir kez hesaplanır.

Arasındaki `IOptionsMonitor` fark `IOptionsSnapshot` ve şudur:

* `IOptionsMonitor`geçerli seçenek değerlerini herhangi bir zamanda alan ve özellikle singleton bağımlılıklarında kullanışlı olan [tek tonluk](xref:fundamentals/dependency-injection#singleton) bir hizmettir.
* `IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduruldýığı sınırdaki seçeneklerin anlık görüntüsünü saÄ `IOptionsSnapshot<T>` lıyor. Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklar ile kullanılmak üzere tasarlanmıştır.

Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* değişikliklerden sonra yeni bir yeninin nasıl oluşturulduğunu gösterir (*Pages/Index.cshtml.cs).* Sunucuya gelen birden çok istek, dosya değiştirilene ve yapılandırma yeniden yüklenene kadar *appsettings.json* dosyası tarafından sağlanan sabit değerleri döndürür.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki resim, `option1` *appsettings.json* dosyasından yüklenen başlangıç ve `option2` değerleri gösterir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*appsettings.json* dosyasındaki değerleri `value1_from_json UPDATED` değiştirin `200`ve . *appsettings.json* dosyasını kaydedin. Seçeneklerin güncelleştirdiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IConfigureNamedOptions ile adlandırılmış seçenekler desteği

Adlandırılmış seçenekler <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> desteği örnek uygulamada Örnek 6 olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenekler yapılandırmaları arasında ayrım yapmasına olanak tanır. Örnek uygulamada, adlandırılmış [seçenekler,](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) [ConfigureNamedOptions\<TOptions> çağıran OptionsServiceCollectionExtensions.Configure ile bildirilir. Uzatma yöntemini yapılandırın.](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) Adlandırılmış seçenekler büyük/küçük harf duyarlıdır.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama ( <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> *Pages/Index.cshtml.cs)* ile adlandırılmış seçeneklere erişir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırılırken, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`değerler, *appsettings.json* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2`değerler tarafından sağlanır:

* Delege `named_options_2` `ConfigureServices` için `Option1`.
* Sınıf tarafından `Option2` sağlanan varsayılan değer. `MyOptions`

## <a name="configure-all-options-with-the-configureall-method"></a>YapılandırmaTüm yöntemiyle tüm seçenekleri yapılandır

Tüm seçenekler örneklerini yöntemle yapılandırın. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örnekleri için yapılandırır. `Startup.ConfigureServices` Yönteme el ile aşağıdaki kodu ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu üretir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Varolan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> `Options.DefaultName` örnekler, örneği hedefleyen olarak kabul `string.Empty`edilir. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>ayrıca <xref:Microsoft.Extensions.Options.IConfigureOptions%601>uygular. Varsayılan uygulama her <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun kullanmak için mantık vardır. `null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek (ve<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanmak) için kullanılır.

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>`TOptions` örnekleri yapılandırmak için kullanılır. `OptionsBuilder`sonraki tüm aramalarda görünmek yerine ilk `AddOptions<TOptions>(string optionsName)` çağrıya yalnızca tek bir parametre olarak adlandırılmış seçenekleri oluşturmayı kolaylaştırır. Seçenekler doğrulaması `ConfigureOptions` ve hizmet bağımlılıklarını kabul eden `OptionsBuilder`aşırı yüklemeler yalnızca .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Seçenekleri yapılandırmak için DI hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken bağımlılık enjeksiyonundan diğer hizmetlere erişebilirsiniz:

* [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) [üzerinde Yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) seçenekleri yapılandırmak için en fazla beş hizmet kullanmanıza olanak sağlayan [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aşırı yükleri sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünüzü oluşturun ve türü hizmet olarak kaydedin.

Bir hizmet oluşturmak daha karmaşık olduğundan, [Yapılandırma'ya](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)bir yapılandırma temsilcisi geçmenizi öneririz. Kendi türünüzü oluşturmak, [Yapıl'ı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)kullandığınızda çerçevenin sizin için yaptığıyla eşdeğerdir. [Yapıyı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) Çağırma, belirtilen genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>hizmet türlerini kabul eden bir oluşturucuya sahip geçici bir genel kayıt kaydeder. 

## <a name="options-validation"></a>Seçenekler doğrulama

Seçenekler doğrulaması, seçenekler yapılandırıldığınızda seçenekleri doğrulamanızı sağlar. Seçenekler `Validate` geçerliyse ve `false` geçerli `true` değilse döndüren bir doğrulama yöntemiyle arama:

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

Önceki örnek, adlandırılmış seçenekler `optionalOptionsName`örneğini . Varsayılan seçenekler örneği. `Options.DefaultName`

Doğrulama, seçenekler örneği oluşturulduğunda çalışır. Bir seçenek örneğinin, ilk erişime de doğrulamayı geçirmesi garanti edilir.

> [!IMPORTANT]
> Seçenekler doğrulaması, seçenekler örneği oluşturulduktan sonra seçenekler intibaklarına karşı koruma zedler. Örneğin, `IOptionsSnapshot` seçenekler ilk erişildiğinde istek başına bir kez oluşturulur ve doğrulanır. Seçenekler, `IOptionsSnapshot` aynı istek için sonraki erişim *denemelerinde*yeniden doğrulanmaz.

Yöntem `Validate` bir `Func<TOptions, bool>`. Doğrulamayı tam olarak özelleştirmek için aşağıdakileri uygulayın: `IValidateOptions<TOptions>`

* Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Başka bir seçenek türüne bağlı doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`Doğrulama:

* Belirli bir adlandırılmış seçenekler örneği.
* Tüm seçenekler `name` `null`ne zaman .

Arabirimi `ValidateOptionsResult` uygulamanızdan bir döndürme:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Veri Ek Açıklama tabanlı doğrulama [microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> yöntemi arayarak `OptionsBuilder<TOptions>`kullanılabilir. `Microsoft.Extensions.Options.DataAnnotations`[Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahildir.

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

Istekli doğrulama (başlangıçta hızlı başarısız) gelecekteki bir sürüm için düşünülmektedir.

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası'nı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>' ile ayarlama Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra yapılandırma sonrası çalışır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>adlı seçenekleri yapılandırmak için kullanılabilir:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlatma sırasında seçeneklere erişim

<xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> hizmetler `Configure` yöntem `Startup.Configure`yürütülmeden önce inşa olduğundan, kullanılabilir.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Kullanmayın <xref:Microsoft.Extensions.Options.IOptions%601> ya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> da `Startup.ConfigureServices`içinde. Hizmet kayıtlarının sıralanması nedeniyle tutarsız bir seçenek durumu oluşabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara yalıtıldığında, uygulama iki önemli yazılım mühendisliği ilkesine bağlıdır:

* Yapılandırma ayarlarına bağlı olan [Arabirim Ayırma İlkesi (ISS) veya Kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Senaryoları (sınıflar), yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* [Uygulamanın](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; farklı bölümleri için Endişelerin Ayrılması Ayarları bağımlı değildir veya birbirine bağlı değildir.

Seçenekler, yapılandırma verilerini doğrulamak için bir mekanizma da sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

[Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun veya [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>seçenekleri almak ve örneğin seçenek bildirimlerini yönetmek için `TOptions` kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Bildirimleri değiştirme
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçici seçenekler geçersiz<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>liği ( )

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenekler örneklerinden sorumludur. Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir yöntemi var. Varsayılan uygulama tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kayıtlı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> alır ve tüm yapılandırmaları ilk olarak çalıştırın, ardından yapılandırma sonrası. Bu ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> sadece <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uygun arabirimi çağırır ayırt eder.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>`TOptions` örnekleri <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> önbelleğe almak için kullanılır. Değerin <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> yeniden hesaplanması için monitördeki seçenekler örneklerini geçersiz kılır (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler ile el ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>tanıtılabilir. Yöntem, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> adlandırılmış tüm örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda yararlıdır. Daha fazla bilgi için [IOptionsSnapshot bölümüyle yapılandırma verilerini yeniden yükle](#reload-configuration-data-with-ioptionssnapshot) bölümüne bakın.

<xref:Microsoft.Extensions.Options.IOptions%601>seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryoları <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>desteklemez. Arabirimi zaten <xref:Microsoft.Extensions.Options.IOptions%601> kullanan ve sağladığı <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>senaryoları gerektirmeyen varolan çerçeveleri ve kitaplıkları kullanmaya devam edebilirsiniz. <xref:Microsoft.Extensions.Options.IOptions%601>

## <a name="general-options-configuration"></a>Genel seçenekler yapılandırması

Genel seçenekler yapılandırması örnek uygulamada Örnek 1 olarak gösterilmiştir.

Bir seçenek sınıfı, ortak parametresiz bir oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıf, `MyOptions`iki özelliği `Option1` vardır `Option2`ve . Varsayılan değerleri ayarlamak isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf `Option1`oluşturucusu varsayılan değerini ayarlar. `Option2`özelliği doğrudan başharfe alarak belirlenen varsayılan bir değere sahiptir (*Modeller/MyOptions.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Sınıf, `MyOptions` yapılandırmaya <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> bağlı ve hizmet kapsayıcısına eklenir:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ayarlara erişmek için [constructor bağımlılık enjeksiyonu](xref:mvc/controllers/dependency-injection) kullanır (*Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appsettings.json* dosyası için `option1` değerleri belirtir `option2`ve:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Ayarlar dosyasından <xref:System.Configuration.ConfigurationBuilder> seçenekler yapılandırmasını yüklemek için özel bir madde kullanırken, temel yolun doğru ayarlandığından onaylayın:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Ayarlar dosyasından seçenekler yapılandırmasını ayarlarken temel yolu açıkça <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlamak gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilci ile basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada Örnek 2 olarak gösterilmiştir.

Seçenekler değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama `MyOptionsWithDelegateConfig` sınıfı kullanır ( Modeller */MyOptionsWithDelegeConfig.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına ikinci bir hizmet eklenir. Bu ile bağlama yapılandırmak için `MyOptionsWithDelegateConfig`bir temsilci kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinden edinilebilir ve kayıtlı oldukları sırada uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her arama <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler. Önceki örnekte, değerleri `Option1` ve `Option2` her ikisi de *appsettings.json*belirtilir, `Option1` `Option2` ancak değerleri ve yapılandırılan temsilci tarafından geçersiz kılındı.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, belirtilen son yapılandırma kaynağı *kazanır* ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenek yapılandırması örnek uygulamada Örnek 3 olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarıyla (sınıflar) ilgili seçenekler sınıfları oluşturmalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümleri yalnızca kullandıkları yapılandırma değerlerine erişebilir.

Seçenekleri yapılandırmaya bağlarken, seçenekler türündeki her özellik formun `property[:sub-property:]`yapılandırma anahtarına bağlıdır. Örneğin, `MyOptions.Option1` özellik `Option1` *appsettings.json*özelliğinden `option1` okunan anahtara bağlıdır.

Aşağıdaki kodda, hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına üçüncü bir hizmet eklenir. *Appsettings.json* dosyasının `MySubOptions` bölümüne `subsection` bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Yöntem `GetSection` <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerektirir.

Örneğin *appsettings.json* dosyası için `subsection` `suboption1` anahtarları olan bir `suboption2`üye tanımlar ve:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Sınıf `MySubOptions` özellikleri tanımlar `SubOption1` ve `SubOption2`, seçenekleri değerlerini tutmak için *(Modeller / MySubOptions.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` yöntemi seçenekleri değerleri *(Pages/Index.cshtml.cs)* ile bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, `OnGet` yöntem alt seçenek sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Bir görünüm modeli veya doğrudan görünüm enjeksiyonu ile sağlanan seçenekler

Bir görünüm modeli veya doğrudan görüntüleme enjeksiyonu ile sağlanan seçenekler örnek uygulamada Örnek 4 olarak gösterilmiştir.

Seçenekler bir görünüm modelinde veya doğrudan <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir görünüme enjekte edilerek sağlanabilir (*Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl enjekte edilebildiğini gösterir:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenekler değerleri işlenen sayfada gösterilir:

![Opsiyon değerleri Option1: value1_from_json ve Option2: -1 modelden ve görünüme enjeksiyon ile yüklenir.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>IOptionsSnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yüklemek örnek uygulamada Örnek 5'te gösterilmiştir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>en az işlem yükü ile yeniden yükleme seçeneklerini destekler.

Seçenekler, isteğin kullanım ömrü boyunca erişildiğinde ve önbelleğe alındığunda istek başına bir kez hesaplanır.

Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* değişikliklerden sonra yeni bir yeninin nasıl oluşturulduğunu gösterir (*Pages/Index.cshtml.cs).* Sunucuya gelen birden çok istek, dosya değiştirilene ve yapılandırma yeniden yüklenene kadar *appsettings.json* dosyası tarafından sağlanan sabit değerleri döndürür.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki resim, `option1` *appsettings.json* dosyasından yüklenen başlangıç ve `option2` değerleri gösterir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*appsettings.json* dosyasındaki değerleri `value1_from_json UPDATED` değiştirin `200`ve . *appsettings.json* dosyasını kaydedin. Seçeneklerin güncelleştirdiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IConfigureNamedOptions ile adlandırılmış seçenekler desteği

Adlandırılmış seçenekler <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> desteği örnek uygulamada Örnek 6 olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenekler yapılandırmaları arasında ayrım yapmasına olanak tanır. Örnek uygulamada, adlandırılmış [seçenekler,](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) [ConfigureNamedOptions\<TOptions> çağıran OptionsServiceCollectionExtensions.Configure ile bildirilir. Uzatma yöntemini yapılandırın.](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) Adlandırılmış seçenekler büyük/küçük harf duyarlıdır.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama ( <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> *Pages/Index.cshtml.cs)* ile adlandırılmış seçeneklere erişir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırılırken, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`değerler, *appsettings.json* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2`değerler tarafından sağlanır:

* Delege `named_options_2` `ConfigureServices` için `Option1`.
* Sınıf tarafından `Option2` sağlanan varsayılan değer. `MyOptions`

## <a name="configure-all-options-with-the-configureall-method"></a>YapılandırmaTüm yöntemiyle tüm seçenekleri yapılandır

Tüm seçenekler örneklerini yöntemle yapılandırın. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örnekleri için yapılandırır. `Startup.ConfigureServices` Yönteme el ile aşağıdaki kodu ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu üretir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Varolan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> `Options.DefaultName` örnekler, örneği hedefleyen olarak kabul `string.Empty`edilir. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>ayrıca <xref:Microsoft.Extensions.Options.IConfigureOptions%601>uygular. Varsayılan uygulama her <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun kullanmak için mantık vardır. `null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek (ve<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanmak) için kullanılır.

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>`TOptions` örnekleri yapılandırmak için kullanılır. `OptionsBuilder`sonraki tüm aramalarda görünmek yerine ilk `AddOptions<TOptions>(string optionsName)` çağrıya yalnızca tek bir parametre olarak adlandırılmış seçenekleri oluşturmayı kolaylaştırır. Seçenekler doğrulaması `ConfigureOptions` ve hizmet bağımlılıklarını kabul eden `OptionsBuilder`aşırı yüklemeler yalnızca .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Seçenekleri yapılandırmak için DI hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken bağımlılık enjeksiyonundan diğer hizmetlere erişebilirsiniz:

* [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) [üzerinde Yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) seçenekleri yapılandırmak için en fazla beş hizmet kullanmanıza olanak sağlayan [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aşırı yükleri sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünüzü oluşturun ve türü hizmet olarak kaydedin.

Bir hizmet oluşturmak daha karmaşık olduğundan, [Yapılandırma'ya](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)bir yapılandırma temsilcisi geçmenizi öneririz. Kendi türünüzü oluşturmak, [Yapıl'ı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)kullandığınızda çerçevenin sizin için yaptığıyla eşdeğerdir. [Yapıyı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) Çağırma, belirtilen genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>hizmet türlerini kabul eden bir oluşturucuya sahip geçici bir genel kayıt kaydeder. 

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası'nı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>' ile ayarlama Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra yapılandırma sonrası çalışır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>adlı seçenekleri yapılandırmak için kullanılabilir:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlatma sırasında seçeneklere erişim

<xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> hizmetler `Configure` yöntem `Startup.Configure`yürütülmeden önce inşa olduğundan, kullanılabilir.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Kullanmayın <xref:Microsoft.Extensions.Options.IOptions%601> ya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> da `Startup.ConfigureServices`içinde. Hizmet kayıtlarının sıralanması nedeniyle tutarsız bir seçenek durumu oluşabilir.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
