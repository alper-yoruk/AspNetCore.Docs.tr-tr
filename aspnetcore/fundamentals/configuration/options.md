---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

--- 
# <a name="options-pattern-in-aspnet-core"></a>ASP.NET Core için seçenek kalıbı

::: moniker range=">= aspnetcore-3.0"

, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT).

Seçenekler stili, ilişkili ayarlar gruplarına kesin olarak belirlenmiş erişim sağlamak için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* [Kaygıları ayırma](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirlerine bağlanmış değil.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a>Hiyerarşik yapılandırmayı bağlama

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptions%601>:

* Şunları ***desteklemez:***
  * Uygulama başladıktan sonra yapılandırma verilerinin okunması.
  * [Adlandırılmış seçenekler](#named)
* [Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:

* Seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [güncelleştirilmiş verileri okumak Için ıoptionssnapshot kullanma](#ios).
* [Kapsamlı](xref:fundamentals/dependency-injection#scoped) olarak kaydedilir ve bu nedenle tek bir hizmete eklenemez.
* [Adlandırılmış seçenekleri](#named) destekler

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:

* , Seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .
* [Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.
* Desteklememektedir
  * Değişiklik bildirimleri
  * [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
  * [Yeniden yüklenebilir yapılandırma](#ios)
  * Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )
  
[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra ayarları veya değiştirme seçeneklerini etkinleştirir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur. Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> . Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası. Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` . , <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a>Güncelleştirilmiş verileri okumak için ıoptionssnapshot kullanın

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır. Güncelleştirilmiş yapılandırma değerlerini okumayı destekleyen yapılandırma sağlayıcıları kullanılırken, yapılandırma değişiklikleri uygulama başladıktan sonra okundu.

Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:

* `IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .
* `IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` . Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.

Aşağıdaki kod kullanır <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` :

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

Yukarıdaki kodda, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okundu.

## <a name="ioptionsmonitor"></a>Ioptionsmonitor

Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` .

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

Aşağıdaki örnek şunu kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a>IController Enamedooptıons kullanarak adlandırılmış seçenekler desteği

Adlandırılmış seçenekler:

* Aynı özelliklere birden çok yapılandırma bölümü bağlandığı zaman faydalıdır.
* Büyük/küçük harfe duyarlıdır.

Aşağıdaki *appSettings. JSON* dosyasını göz önünde bulundurun:

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

Ve bağlamak için iki sınıf oluşturmak yerine `TopItem:Month` `TopItem:Year` , her bölüm için aşağıdaki sınıf kullanılır:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

Aşağıdaki kod, adlandırılmış seçenekleri yapılandırır:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

Aşağıdaki kod, adlandırılmış seçenekleri gösterir:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

Tüm seçenekler adlandırılmış örneklerdir. <xref:Microsoft.Extensions.Options.IConfigureOptions%601>örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır. `null`Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın.

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak için kullanılır `TOptions` . `OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` . Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .

`OptionsBuilder`[Seçenekler doğrulama](#val) bölümünde kullanılır.

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, hizmetlere bağımlılık ekleme işleminden erişilebilir:

* [Options Builder \< TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. `OptionsBuilder<TOptions>`, seçenekleri yapılandırmak için en fazla beş hizmet kullanılmasına izin veren [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Veya uygulayan bir tür oluşturun <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ve türü bir hizmet olarak kaydedin.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Bir tür oluşturmak,, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)çağrılırken çerçevenin yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder. 

<a name="val"></a>

## <a name="options-validation"></a>Seçenekler doğrulaması

Seçenekler doğrulaması seçenek değerlerinin doğrulanmasını sağlar.

Aşağıdaki *appSettings. JSON* dosyasını göz önünde bulundurun:

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

Aşağıdaki sınıf `"MyConfig"` yapılandırma bölümüne bağlanır ve birkaç `DataAnnotations` kural uygular:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

Aşağıdaki kod, <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> sınıfa bağlanan ve doğrulamayı sağlayan bir [Options builder \< TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) almak için çağırır `MyConfigOptions` `DataAnnotations` :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

Aşağıdaki kod yapılandırma değerlerini veya doğrulama hatalarını görüntüler:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

Aşağıdaki kod, bir temsilci kullanarak daha karmaşık bir doğrulama kuralı uygular:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a>Karmaşık doğrulama için ıvalidateoptions

Aşağıdaki sınıf şunları uygular <xref:Microsoft.Extensions.Options.IValidateOptions`1> :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

`IValidateOptions`doğrulama kodunu `StartUp` bir sınıfa ve sınıfına taşımaya izin vermez.

Önceki kodu kullanarak, doğrulama ' de `Startup.ConfigureServices` aşağıdaki kodla etkinleştirilir:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:

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

<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601>ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` . Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

## <a name="optionsconfigurationextensions-nuget-package"></a>Options. ConfigurationExtensions NuGet paketi

[Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine ASP.NET Core uygulamalarında örtük olarak başvurulur.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* [Kaygıları ayırma](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirlerine bağlanmış değil.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur. Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> . Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası. Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` . , <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> . <xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` . Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` . `Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> (*Pages/Index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appSettings. JSON* dosyası ve değerlerini belirtir `option1` `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
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
> İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir. Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler. Yukarıdaki örnekte, `Option1` ve değerleri `Option2` *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` . Örneğin, `MyOptions.Option1` özelliği `Option1` `option1` *appSettings. JSON*içindeki özelliğinden okunan anahtara bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir. `MySubOptions` `subsection` *AppSettings. JSON* dosyasının bölümüne bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.

Örnek *appSettings. JSON* dosyası `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için (*modeller/myalt seçenekler. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Seçeneklere ekleme

Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.

Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :

* RazorYönergeyle bir sayfa veya MVC görünümü [`@inject`](xref:mvc/views/razor#inject) Razor .
* Bir sayfa veya görünüm modeli.

Örnek uygulamadan alınan aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline (*Sayfalar/Index. cshtml. cs*) sahiptir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.

Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:

* `IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .
* `IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` . Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.

Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appSettings. JSON* değişikliklerinden sonra yeni bir oluşturma Işlemi gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, `option1` `option2` *appSettings. JSON* dosyasından yüklenen ilk ve değerler gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile değiştirin `200` . *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [configurenamedooptıons \< TOptions > çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın. Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.

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

* `named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .
* `Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır. Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :

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
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır. Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak için kullanılır `TOptions` . `OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` . Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder \< TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. [Seçenekseçenekleri Oluşturucusu \< TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak sağlayan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder. 

## <a name="options-validation"></a>Seçenekler doğrulaması

Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar. `Validate` `true` Seçenekler geçerliyse ve geçerli değillerse, döndüren bir doğrulama yöntemiyle çağırın `false` :

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

Önceki örnekte, adlandırılmış seçenekler örneği olarak ayarlanır `optionalOptionsName` . Varsayılan Seçenekler örneği `Options.DefaultName` .

Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır. Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.

> [!IMPORTANT]
> Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz. Örneğin, Seçenekler `IOptionsSnapshot` ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır. Bu `IOptionsSnapshot` Seçenekler *, aynı istek için*sonraki erişim denemelerinde yeniden doğrulanmaz.

`Validate`Yöntemi bir kabul eder `Func<TOptions, bool>` . Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>` izin veren uygulayın:

* Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Başka bir seçenek türüne bağlı olan doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`doğrular

* Belirli bir adlandırılmış seçenekler örneği.
* Olduğunda tüm seçenekler `name` `null` .

Arabirimini uygulamanızdan döndürün `ValidateOptionsResult` :

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Veri eki tabanlı doğrulama, üzerinde yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>` . `Microsoft.Extensions.Options.DataAnnotations`, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.

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

Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:

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

<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601>ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` . Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır. [Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:

* Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.
* [Kaygıları ayırma](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirlerine bağlanmış değil.

Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar. Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.

## <a name="options-interfaces"></a>Seçenekler arabirimleri

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:

* Değişiklik bildirimleri
* [Adlandırılmış seçenekler](#named-options-support-with-iconfigurenamedoptions)
* [Yeniden yüklenebilir yapılandırma](#reload-configuration-data-with-ioptionssnapshot)
* Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )

[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur. Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> . Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası. Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` . , <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır. Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.

<xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir. Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> . <xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .

## <a name="general-options-configuration"></a>Genel Seçenekler yapılandırması

Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.

Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır. Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` . Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` . `Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> (*Pages/Index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Örneğin *appSettings. JSON* dosyası ve değerlerini belirtir `option1` `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:
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
> İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

## <a name="configure-simple-options-with-a-delegate"></a>Bir temsilciyle basit seçenekleri yapılandırma

Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.

Seçenek değerlerini ayarlamak için bir temsilci kullanın. Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir. Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz. Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler. Yukarıdaki örnekte, `Option1` ve değerleri `Option2` *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.

Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar. Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Alt seçenekler yapılandırması

Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.

Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır. Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.

Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` . Örneğin, `MyOptions.Option1` özelliği `Option1` `option1` *appSettings. JSON*içindeki özelliğinden okunan anahtara bağlanır.

Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir. `MySubOptions` `subsection` *AppSettings. JSON* dosyasının bölümüne bağlanır:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.

Örnek *appSettings. JSON* dosyası `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için (*modeller/myalt seçenekler. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler

Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada 4 olarak gösterilmiştir.

Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme

Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.

Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.

Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appSettings. JSON* değişikliklerinden sonra yeni bir oluşturma Işlemi gösterir (*Pages/Index. cshtml. cs*). Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Aşağıdaki görüntüde, `option1` `option2` *appSettings. JSON* dosyasından yüklenen ilk ve değerler gösterilmektedir:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile değiştirin `200` . *AppSettings. JSON* dosyasını kaydedin. Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>IController Enamedooptıons ile adlandırılmış seçenekler desteği

İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.

Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir. Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [configurenamedooptıons \< TOptions > çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın. Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.

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

* `named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .
* `Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .

## <a name="configure-all-options-with-the-configureall-method"></a>Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma

Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın. Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır. Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :

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
> Tüm seçenekler adlandırılmış örneklerdir. Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır. Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).

## <a name="optionsbuilder-api"></a>Seçenekno Oluşturucu API 'SI

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak için kullanılır `TOptions` . `OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` . Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ayarları yapılandırmak için dı hizmetlerini kullanma

Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:

* [Options Builder \< TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin. [Seçenekseçenekleri Oluşturucusu \< TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak sağlayan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.

Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz. Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir. [Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder. 

## <a name="options-post-configuration"></a>Yapılandırma sonrası seçenekler

Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:

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

<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Başlangıç sırasında seçeneklere erişme

<xref:Microsoft.Extensions.Options.IOptions%601>ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` . Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
