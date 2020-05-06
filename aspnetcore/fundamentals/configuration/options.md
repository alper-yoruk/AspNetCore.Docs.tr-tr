---
title: ASP.NET Core için seçenek kalıbı
author: rick-anderson
description: ASP.NET Core uygulamalarında ilgili ayarların gruplarını temsil etmek için seçenekler deseninin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: efce2caf37534823016c12b298afd277bab22030
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769942"
---
# <a name="options-pattern-in-aspnet-core"></a>ASP.NET Core için seçenek kalıbı

::: moniker range=">= aspnetcore-3.0"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* Uygulamanın farklı parçaları için sorun &ndash; ayarlarının [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı değildir veya birbirlerine aktarılmaz.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="package"></a>Paket

[Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine ASP.NET Core uygulamalarında örtük olarak başvurulur.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için `TOptions` seçenek bildirimlerini yönetmek için kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur. Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir metodu vardır. Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası. Ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, örnekleri önbelleğe <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `TOptions` almak için tarafından kullanılır. , <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>el ile tanıtılamaz. Yöntemi <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> , tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Zaten <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz.

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıfının, `MyOptions`, `Option1` ve `Option2`iki özelliği vardır. Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1`. `Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Sınıfı, ile <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlarına erişmek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Pages/Index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin `option1` `option2` *appSettings. JSON* dosyası ve değerlerini belirtir:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Bir ayarlar dosyasından yükleme <xref:System.Configuration.ConfigurationBuilder> seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
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
> İle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir. Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> , hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler. Yukarıdaki örnekte, `Option1` ve `Option2` değerleri *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun `property[:sub-property:]`bir yapılandırma anahtarına bağlanır. Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan anahtara `Option1`bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir. `MySubOptions` *AppSettings. JSON* dosyasının bölümüne `subsection` bağlanır:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.

Örnek *appSettings. JSON* dosyası ve `subsection` `suboption1` `suboption2`için anahtarlar içeren bir üyeyi tanımlar:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2`seçenekler değerlerini tutmak Için (*modeller/myalt seçenekler. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, `OnGet` Yöntem, alt sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Seçeneklere ekleme

Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.

Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :

* Yönergeyle Razor [`@inject`](xref:mvc/views/razor#inject) bir sayfa veya MVC görünümü. Razor
* Bir sayfa veya görünüm modeli.

Örnek uygulamadan <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> alınan aşağıdaki örnek, bir sayfa modeline (*Sayfalar/Index. cshtml. cs*) sahiptir:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl ekleneceğini gösterir:

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.

' <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.

Ve `IOptionsMonitor` `IOptionsSnapshot` arasındaki fark şudur:

* `IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .
* `IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve `IOptionsSnapshot<T>` nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar. Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.

Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> sonra yeni bir oluşturma işlemi gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, *appSettings. JSON* dosyasından `option2` yüklenen ilk `option1` ve değerler gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile `200`değiştirin. *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

İle <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> adlandırılmış seçenek desteği örnek uygulamada 6 örnek olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [\<configurenamedooptıons TOptions> çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın. Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2`değerleri tarafından sağlanır:

* İçin `named_options_2` `Option1`içindeki `ConfigureServices` temsilci.
* `MyOptions` Sınıfı tarafından `Option2` sağlanacak varsayılan değer.

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, ortak `Option1` bir değere sahip tüm yapılandırma örneklerini yapılandırır. `Startup.ConfigureServices` Yöntemine el ile aşağıdaki kodu ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, `Options.DefaultName` örneği hedefleme olarak değerlendirilir `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Varsayılan uygulamasının, <xref:Microsoft.Extensions.Options.IOptionsFactory%601> her birini uygun şekilde kullanma mantığı vardır. `null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak `TOptions` için kullanılır. `OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk `AddOptions<TOptions>(string optionsName)` çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır. Seçenekler doğrulaması ve hizmet `ConfigureOptions` bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla `OptionsBuilder`kullanılabilir.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. `OptionsBuilder<TOptions>`, seçenekleri yapılandırmak için en fazla beş hizmet kullanılmasına izin veren [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* ' İ uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> hizmet olarak türü kaydeden kendi türünü oluşturun.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder. 

## <a name="options-validation"></a>Seçenekler doğrulaması

Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar. Seçenekler `Validate` geçerliyse ve `false` geçerli değillerse, döndüren `true` bir doğrulama yöntemiyle çağırın:

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

Önceki örnekte, adlandırılmış seçenekler örneği olarak `optionalOptionsName`ayarlanır. Varsayılan Seçenekler örneği `Options.DefaultName`.

Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır. Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.

> [!IMPORTANT]
> Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz. Örneğin, `IOptionsSnapshot` seçenekler ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır. Bu `IOptionsSnapshot` Seçenekler *, aynı istek için*sonraki erişim denemelerinde yeniden doğrulanmaz.

`Validate` Yöntemi bir `Func<TOptions, bool>`kabul eder. Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>`izin veren uygulayın:

* Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Başka bir seçenek türüne bağlı olan doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`doğrular

* Belirli bir adlandırılmış seçenekler örneği.
* Olduğunda `name` tüm seçenekler `null`.

`ValidateOptionsResult` Arabirimini uygulamanızdan döndürün:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Veri eki tabanlı doğrulama, üzerinde <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>`yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir. `Microsoft.Extensions.Options.DataAnnotations`ASP.NET Core uygulamalarda örtülü olarak başvurulur.

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

Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:

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

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' de, Hizmetler `Startup.Configure` `Configure` Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Veya <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> içinde `Startup.ConfigureServices`kullanmayın. Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* Uygulamanın farklı parçaları için sorun &ndash; ayarlarının [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı değildir veya birbirlerine aktarılmaz.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için `TOptions` seçenek bildirimlerini yönetmek için kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur. Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir metodu vardır. Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası. Ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, örnekleri önbelleğe <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `TOptions` almak için tarafından kullanılır. , <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>el ile tanıtılamaz. Yöntemi <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> , tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Zaten <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz.

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıfının, `MyOptions`, `Option1` ve `Option2`iki özelliği vardır. Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1`. `Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Sınıfı, ile <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlarına erişmek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Pages/Index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin `option1` `option2` *appSettings. JSON* dosyası ve değerlerini belirtir:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Bir ayarlar dosyasından yükleme <xref:System.Configuration.ConfigurationBuilder> seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
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
> İle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir. Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> , hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler. Yukarıdaki örnekte, `Option1` ve `Option2` değerleri *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun `property[:sub-property:]`bir yapılandırma anahtarına bağlanır. Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan anahtara `Option1`bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir. `MySubOptions` *AppSettings. JSON* dosyasının bölümüne `subsection` bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.

Örnek *appSettings. JSON* dosyası ve `subsection` `suboption1` `suboption2`için anahtarlar içeren bir üyeyi tanımlar:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2`seçenekler değerlerini tutmak Için (*modeller/myalt seçenekler. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, `OnGet` Yöntem, alt sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Seçeneklere ekleme

Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.

Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :

* Yönergeyle Razor [`@inject`](xref:mvc/views/razor#inject) bir sayfa veya MVC görünümü. Razor
* Bir sayfa veya görünüm modeli.

Örnek uygulamadan <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> alınan aşağıdaki örnek, bir sayfa modeline (*Sayfalar/Index. cshtml. cs*) sahiptir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl ekleneceğini gösterir:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.

' <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.

Ve `IOptionsMonitor` `IOptionsSnapshot` arasındaki fark şudur:

* `IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .
* `IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve `IOptionsSnapshot<T>` nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar. Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.

Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> sonra yeni bir oluşturma işlemi gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, *appSettings. JSON* dosyasından `option2` yüklenen ilk `option1` ve değerler gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile `200`değiştirin. *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

İle <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> adlandırılmış seçenek desteği örnek uygulamada 6 örnek olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [\<configurenamedooptıons TOptions> çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın. Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2`değerleri tarafından sağlanır:

* İçin `named_options_2` `Option1`içindeki `ConfigureServices` temsilci.
* `MyOptions` Sınıfı tarafından `Option2` sağlanacak varsayılan değer.

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, ortak `Option1` bir değere sahip tüm yapılandırma örneklerini yapılandırır. `Startup.ConfigureServices` Yöntemine el ile aşağıdaki kodu ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, `Options.DefaultName` örneği hedefleme olarak değerlendirilir `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Varsayılan uygulamasının, <xref:Microsoft.Extensions.Options.IOptionsFactory%601> her birini uygun şekilde kullanma mantığı vardır. `null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak `TOptions` için kullanılır. `OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk `AddOptions<TOptions>(string optionsName)` çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır. Seçenekler doğrulaması ve hizmet `ConfigureOptions` bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla `OptionsBuilder`kullanılabilir.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. [Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* ' İ uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> hizmet olarak türü kaydeden kendi türünü oluşturun.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder. 

## <a name="options-validation"></a>Seçenekler doğrulaması

Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar. Seçenekler `Validate` geçerliyse ve `false` geçerli değillerse, döndüren `true` bir doğrulama yöntemiyle çağırın:

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

Önceki örnekte, adlandırılmış seçenekler örneği olarak `optionalOptionsName`ayarlanır. Varsayılan Seçenekler örneği `Options.DefaultName`.

Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır. Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.

> [!IMPORTANT]
> Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz. Örneğin, `IOptionsSnapshot` seçenekler ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır. Bu `IOptionsSnapshot` Seçenekler *, aynı istek için*sonraki erişim denemelerinde yeniden doğrulanmaz.

`Validate` Yöntemi bir `Func<TOptions, bool>`kabul eder. Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>`izin veren uygulayın:

* Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Başka bir seçenek türüne bağlı olan doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`doğrular

* Belirli bir adlandırılmış seçenekler örneği.
* Olduğunda `name` tüm seçenekler `null`.

`ValidateOptionsResult` Arabirimini uygulamanızdan döndürün:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Veri eki tabanlı doğrulama, üzerinde <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>`yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir. `Microsoft.Extensions.Options.DataAnnotations`, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.

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

Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:

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

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' de, Hizmetler `Startup.Configure` `Configure` Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Veya <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> içinde `Startup.ConfigureServices`kullanmayın. Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* Uygulamanın farklı parçaları için sorun &ndash; ayarlarının [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı değildir veya birbirlerine aktarılmaz.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için `TOptions` seçenek bildirimlerini yönetmek için kullanılır. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur. Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir metodu vardır. Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası. Ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, örnekleri önbelleğe <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `TOptions` almak için tarafından kullanılır. , <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Değerler ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>el ile tanıtılamaz. Yöntemi <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> , tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Zaten <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz.

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıfının, `MyOptions`, `Option1` ve `Option2`iki özelliği vardır. Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1`. `Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Sınıfı, ile <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlarına erişmek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Pages/Index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin `option1` `option2` *appSettings. JSON* dosyası ve değerlerini belirtir:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Bir ayarlar dosyasından yükleme <xref:System.Configuration.ConfigurationBuilder> seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
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
> İle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir. Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> , hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler. Yukarıdaki örnekte, `Option1` ve `Option2` değerleri *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun `property[:sub-property:]`bir yapılandırma anahtarına bağlanır. Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan anahtara `Option1`bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir. `MySubOptions` *AppSettings. JSON* dosyasının bölümüne `subsection` bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.

Örnek *appSettings. JSON* dosyası ve `subsection` `suboption1` `suboption2`için anahtarlar içeren bir üyeyi tanımlar:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2`seçenekler değerlerini tutmak Için (*modeller/myalt seçenekler. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, `OnGet` Yöntem, alt sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler

Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada 4 olarak gösterilmiştir.

Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl ekleneceğini gösterir:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.

Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.

Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> sonra yeni bir oluşturma işlemi gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, *appSettings. JSON* dosyasından `option2` yüklenen ilk `option1` ve değerler gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile `200`değiştirin. *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

İle <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> adlandırılmış seçenek desteği örnek uygulamada 6 örnek olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [\<configurenamedooptıons TOptions> çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın. Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır. `named_options_2`değerleri tarafından sağlanır:

* İçin `named_options_2` `Option1`içindeki `ConfigureServices` temsilci.
* `MyOptions` Sınıfı tarafından `Option2` sağlanacak varsayılan değer.

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, ortak `Option1` bir değere sahip tüm yapılandırma örneklerini yapılandırır. `Startup.ConfigureServices` Yöntemine el ile aşağıdaki kodu ekleyin:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, `Options.DefaultName` örneği hedefleme olarak değerlendirilir `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Varsayılan uygulamasının, <xref:Microsoft.Extensions.Options.IOptionsFactory%601> her birini uygun şekilde kullanma mantığı vardır. `null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak `TOptions` için kullanılır. `OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk `AddOptions<TOptions>(string optionsName)` çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır. Seçenekler doğrulaması ve hizmet `ConfigureOptions` bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla `OptionsBuilder`kullanılabilir.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. [Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* ' İ uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> hizmet olarak türü kaydeden kendi türünü oluşturun.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder. 

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:

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

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' de, Hizmetler `Startup.Configure` `Configure` Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Veya <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> içinde `Startup.ConfigureServices`kullanmayın. Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
