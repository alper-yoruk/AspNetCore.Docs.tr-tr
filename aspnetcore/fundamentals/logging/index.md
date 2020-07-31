---
title: .NET Core ve ASP.NET Core'da günlük
author: rick-anderson
description: Microsoft. Extensions. Logging NuGet paketi tarafından sunulan günlüğe kaydetme çerçevesini nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330769"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core ve ASP.NET Core'da günlük

::: moniker range=">= aspnetcore-3.0"

[Kirk Larkabağı](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler. Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.

Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalardan oluşur. Bu kod parçacıklarının günlüğe kaydetmeye özgü bölümleri, [genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanan tüm .NET Core uygulamaları için geçerlidir. ASP.NET Core Web uygulaması şablonları genel Konağı kullanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>Günlüğe kaydetme sağlayıcıları

Günlükleri görüntüleyen sağlayıcı dışında günlüğe kaydetme sağlayıcıları mağaza günlükleri `Console` . Örneğin, Azure Application Insights sağlayıcısı günlükleri Azure Application Insights depolar. Birden çok sağlayıcı etkinleştirilebilir.

Varsayılan ASP.NET Core Web uygulaması şablonları:

* [Genel Konağı](xref:fundamentals/host/generic-host)kullanın.
* <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Aşağıdaki günlük sağlayıcılarını ekleyen çağırın:
  * [Konsol](#console)
  * [Hata ayıklama](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): yalnızca Windows

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

Yukarıdaki kod, `Program` ASP.NET Core Web uygulaması şablonlarıyla oluşturulan sınıfı gösterir. Sonraki birkaç bölüm, genel ana bilgisayarı kullanan ASP.NET Core Web uygulaması şablonlarına göre örnekler sağlar. [Konak olmayan konsol uygulamaları](#nhca) bu belgenin ilerleyen kısımlarında ele alınmıştır.

Tarafından eklenen varsayılan günlük sağlayıcıları kümesini geçersiz kılmak için `Host.CreateDefaultBuilder` `ClearProviders` gerekli günlük sağlayıcılarını çağırın ve ekleyin. Örneğin, aşağıdaki kod:

* <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>Oluşturucunun tüm örneklerini kaldırmak için çağırır <xref:Microsoft.Extensions.Logging.ILoggerProvider> .
* [Konsol](#console) günlüğü sağlayıcısını ekler.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

Ek sağlayıcılar için bkz.:

* [Yerleşik günlük oluşturma sağlayıcıları](#bilp)
* [Üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers).

## <a name="create-logs"></a>Günlükleri oluştur

Günlükler oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) öğesinden bir nesne kullanın.

Aşağıdaki örnek:

* `ILogger<AboutModel>`Türün tam adı olan bir günlük *kategorisini* kullanan bir günlükçü oluşturur `AboutModel` . Günlük kategorisi, her günlük ile ilişkili bir dizedir.
* Bu <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> düzeyde günlüğe kaydedilecek çağrılar `Information` . Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

[Düzeyler](#log-level) ve [Kategoriler](#log-category) bu belgenin ilerleyen bölümlerinde daha ayrıntılı açıklanmıştır.

Hakkında bilgi için Blazor , bkz. bu belgede [Günlükler Blazor Blazor WebAssembly oluşturma](#clib) .

[Ana ve başlangıçta Günlükler oluşturma](#clms) , ve ' de günlüklerin nasıl oluşturulacağını `Main` gösterir `Startup` .

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Günlüğe kaydetme yapılandırması genellikle `Logging` *appSettings*'in bölümü tarafından sağlanır. `{Environment}` *. JSON* dosyaları. Dosyasında aşağıdaki *appsettings.Development.js* ASP.NET Core Web uygulaması şablonları tarafından oluşturulur:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

Önceki JSON 'da:

* `"Default"`, `"Microsoft"` Ve `"Microsoft.Hosting.Lifetime"` kategorileri belirtilir.
* `"Microsoft"`Kategori, ile başlayan tüm kategoriler için geçerlidir `"Microsoft"` . Örneğin, bu ayar kategori için geçerlidir `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` .
* `"Microsoft"`Günlük düzeyindeki `Warning` ve daha yüksek kategori günlükleri.
* Kategori kategorisinden `"Microsoft.Hosting.Lifetime"` daha özeldir `"Microsoft"` , bu nedenle `"Microsoft.Hosting.Lifetime"` Kategori "Information" ve üzeri günlük düzeyinde günlüğe kaydedilir.
* Belirli bir günlük sağlayıcısı belirtilmedi, bu nedenle `LogLevel` [Windows olay](#welog)günlüğü hariç tüm etkin günlük sağlayıcıları için geçerlidir.

`Logging`Özelliği <xref:Microsoft.Extensions.Logging.LogLevel> ve günlük sağlayıcısı özelliklerine sahip olabilir. `LogLevel`Seçili kategoriler için günlüğe kaydedilecek en düşük [düzeyi](#log-level) belirtir. Önceki JSON 'da `Information` ve `Warning` günlük düzeyleri belirtilmiştir. `LogLevel`Günlüğün önem derecesini ve 0 ile 6 arasındaki aralıkları belirtir:

`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 ve `None` = 6.

Bir `LogLevel` belirtildiğinde, belirtilen düzeydeki ve daha yüksek iletiler için günlüğe kaydetme etkinleştirilir. Önceki JSON 'da `Default` Kategori, ve üzeri için günlüğe kaydedilir `Information` . Örneğin,, `Information` , `Warning` `Error` ve `Critical` iletileri günlüğe kaydedilir. Hayır `LogLevel` belirtilmemişse, günlüğe kaydetme varsayılan olarak `Information` düzeydir. Daha fazla bilgi için bkz. [günlük düzeyleri](#llvl).

Sağlayıcı özelliği, bir özelliği belirtebilir `LogLevel` . `LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir ve sağlayıcı olmayan günlük ayarlarını geçersiz kılar. Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

`Logging.{providername}.LogLevel`' Deki geçersiz kılma ayarlarındaki ayarlar `Logging.LogLevel` . Önceki JSON 'da, `Debug` sağlayıcının varsayılan günlük düzeyi şu şekilde ayarlanır `Information` :

`Logging:Debug:LogLevel:Default:Information`

Yukarıdaki ayar, `Information` hariç her kategori için günlük düzeyini belirtir `Logging:Debug:` `Microsoft.Hosting` . Belirli bir kategori listelendiğinde, belirtilen kategori varsayılan kategoriyi geçersiz kılar. Önceki JSON 'da `Logging:Debug:LogLevel` Kategoriler `"Microsoft.Hosting"` ve `"Default"` içindeki ayarları geçersiz kılın`Logging:LogLevel`

En düşük günlük düzeyi şu şekilde belirtilebilir:

* Belirli sağlayıcılar: Örneğin,`Logging:EventSource:LogLevel:Default:Information`
* Belirli Kategoriler: Örneğin,`Logging:LogLevel:Microsoft:Warning`
* Tüm sağlayıcılar ve tüm Kategoriler:`Logging:LogLevel:Default:Warning`

Minimum düzeyin altındaki tüm Günlükler şu ***değildir***:

* Sağlayıcıya geçirildi.
* Günlüğe kaydedilir veya gösterilir.

Tüm günlükleri gizlemek için [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel)belirtin. `LogLevel.None`6 değerine sahiptir `LogLevel.Critical` (5).

Bir sağlayıcı, [günlük kapsamlarını](#logscopes)destekliyorsa, etkinleştirilip etkinleştirilmeyeceğini `IncludeScopes` belirtir. Daha fazla bilgi için bkz. [günlük kapsamları](#logscopes)

Aşağıdaki *appsettings.js* dosyası varsayılan olarak etkinleştirilen tüm sağlayıcıları içerir:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

Yukarıdaki örnekte:

* Kategoriler ve düzeyler önerilen değerler değildir. Varsayılan tüm sağlayıcıları göstermek için örnek sağlanır.
* `Logging.{providername}.LogLevel`' Deki geçersiz kılma ayarlarındaki ayarlar `Logging.LogLevel` . Örneğin, içindeki düzeyi `Debug.LogLevel.Default` içindeki düzeyi geçersiz kılar `LogLevel.Default` .
* Her varsayılan sağlayıcı *diğer adı* kullanılır. Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *diğer ad* tanımlar. Yerleşik sağlayıcılar diğer adları şunlardır:
  * Konsol
  * Hata ayıklama
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>Günlük düzeyini komut satırı, ortam değişkenleri ve diğer yapılandırma ile ayarlama

Günlük düzeyi herhangi bir [yapılandırma sağlayıcısından](xref:fundamentals/configuration/index)ayarlanabilir. 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Aşağıdaki komutlar:

* Ortam anahtarını `Logging:LogLevel:Microsoft` Windows üzerinde bir değeri olarak ayarlayın `Information` .
* ASP.NET Core Web uygulaması şablonlarıyla oluşturulan bir uygulama kullanırken ayarları test edin. `dotnet run`Komutun kullanıldıktan sonra proje dizininde çalıştırılması gerekir `set` .

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

Önceki ortam ayarı:

* Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.
* Visual Studio ile başlatılan tarayıcılarda okunamaz.

Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutu, Windows 'daki ortam anahtarını ve değerini de ayarlar. Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir. `/M`Anahtar, değişkeni sistem ortamında ayarlar. `/M`Kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin. Azure App Service uygulama ayarları şunlardır:

* Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.
* Ortam değişkenleri olarak sunulur.

Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Ortam değişkenlerini kullanarak ASP.NET Core yapılandırma değerlerini ayarlama hakkında daha fazla bilgi için bkz. [ortam değişkenleri](xref:fundamentals/configuration/index#environment-variables). Komut satırı, Azure Key Vault, Azure Uygulama yapılandırması, diğer dosya biçimleri ve daha fazlası dahil diğer yapılandırma kaynaklarını kullanma hakkında bilgi için bkz <xref:fundamentals/configuration/index> ..

## <a name="how-filtering-rules-are-applied"></a>Filtreleme kuralları nasıl uygulanır

Bir <xref:Microsoft.Extensions.Logging.ILogger%601> nesne oluşturulduğunda, <xref:Microsoft.Extensions.Logging.ILoggerFactory> nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer. Bir örnek tarafından yazılan tüm iletiler `ILogger` , seçilen kurallara göre filtrelenmiştir. Her sağlayıcı ve kategori çifti için en özel kural, kullanılabilir kurallardan seçilir.

Belirli bir kategori için oluşturulan her sağlayıcı için aşağıdaki algoritma kullanılır `ILogger` :

* Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin. Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.
* Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin. Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.
* Birden çok kural seçilirse, **son** olanı götürün.
* Hiçbir kural seçilmezse, kullanın `MinimumLevel` .

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>DotNet Run ve Visual Studio 'dan çıktıyı günlüğe kaydetme

[Varsayılan günlük oluşturma sağlayıcılarıyla](#lp) oluşturulan Günlükler görüntülenir:

* Visual Studio’da
  * Hata ayıklama sırasında hata ayıkla çıkış penceresinde.
  * ASP.NET Core Web sunucusu penceresinde.
* Konsol penceresinde, uygulama ile birlikte çalıştırıldığında `dotnet run` .

"Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır. ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanır.

<a name="lcat"></a>

## <a name="log-category"></a>Günlük kategorisi

Bir `ILogger` nesne oluşturulduğunda, bir *Kategori* belirtilir. Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger` . Kategori dizesi rastgele, ancak kural sınıf adını kullanmaktır. Örneğin, bir denetleyicide adı olabilir `"TodoApi.Controllers.TodoController"` . ASP.NET Core Web Apps, `ILogger<T>` `ILogger` Kategori olarak tam nitelikli tür adını kullanan bir örneği otomatik olarak almak için kullanır `T` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>`, `CreateLogger` tam nitelikli tür adı ile çağırma ile eşdeğerdir `T` .

<a name="llvl"></a>

## <a name="log-level"></a>Günlük düzeyi

Aşağıdaki tabloda <xref:Microsoft.Extensions.Logging.LogLevel> değerler, kolaylık `Log{LogLevel}` genişletme yöntemi ve önerilen kullanım listelenmektedir:

| LogLevel | Değer | Yöntem | Açıklama |
| -------- | ----- | ------ | ----------- |
| [İzleme](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | En ayrıntılı iletileri içerir. Bu iletilerde hassas uygulama verileri bulunabilir. Bu iletiler varsayılan olarak devre dışıdır ve üretimde ***etkinleştirilmemelidir.*** |
| [Hata ayıklama](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | Hata ayıklama ve geliştirme için. Yüksek hacimden dolayı üretimde dikkatli olarak kullanın. |
| [Bilgi](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | Uygulamanın genel akışını izler. Uzun süreli bir değere sahip olabilir. |
| [Uyarı](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | Olağandışı veya beklenmeyen olaylar için. Genellikle, uygulamanın başarısız olmasına neden olmayan hataları veya koşulları içerir. |
| [Hata](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | İşlenemeyen hatalar ve özel durumlar için. Bu iletiler, uygulama genelinde bir hata değil, geçerli işlemde veya istekte bir hata olduğunu gösterir. |
| [Kritik](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | Anında ilgilenilmesi gereken hatalarda. Örnekler: veri kaybı senaryoları, disk alanı yetersiz. |
| [Hiçbiri](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | Günlüğe kaydetme kategorisinin herhangi bir ileti yazmamalıdır. |

Önceki tabloda, `LogLevel` En düşük önem derecesine göre listelenmiştir.

[Günlük](xref:Microsoft.Extensions.Logging.LoggerExtensions) yönteminin ilk parametresi, <xref:Microsoft.Extensions.Logging.LogLevel> Günlüğün önem derecesini gösterir. Çağırmak yerine `Log(LogLevel, ...)` , çoğu Geliştirici [günlük {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) uzantı yöntemlerini çağırır. `Log{LogLevel}`Uzantı yöntemleri [günlük yöntemini çağırır ve LogLevel değerini belirtir](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs). Örneğin, aşağıdaki iki günlük çağrısı işlevsel olarak eşdeğerdir ve aynı günlüğü üretir:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem`olay KIMLIĞIDIR. `MyLogEvents`, örnek uygulamanın bir parçasıdır ve [günlük olay kimliği](#leid) bölümünde görüntülenir.

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Yukarıdaki kodda, ilk `Log{LogLevel}` parametresi `MyLogEvents.GetItem` [günlük olay kimliğidir](#leid). İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur. Yöntem parametreleri bu belgenin ilerleyen kısımlarında bulunan [ileti şablonu](#lmt) bölümünde açıklanmaktadır.

`Log{LogLevel}`Belirli bir depolama ortamına ne kadar günlük çıktısı yazıldığını denetlemek için uygun yöntemi çağırın. Örneğin:

* Üretimde:
  * Veya düzeylerinde günlüğe kaydetme, `Trace` `Information` yüksek hacimli ayrıntılı günlük iletileri oluşturur. Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, `Trace` `Information` iletileri yüksek hacimli ve düşük maliyetli bir veri deposuna günlüğe kaydedin. `Trace`Belirli kategorileri ve sınırlamayı değerlendirin `Information` .
  * Düzeylerde oturum açma, `Warning` `Critical` birkaç günlük iletisi üretmelidir.
    * Maliyetler ve depolama sınırları genellikle bir sorun değildir.
    * Birkaç günlük veri deposu seçimlerine daha fazla esneklik sağlar.
* Geliştirme aşamasında:
  * `Warning` olarak ayarlayın.
  * `Trace` `Information` Sorun giderirken veya ileti ekleyin. Çıktıyı sınırlandırmak için, `Trace` `Information` yalnızca araştırma bölümündeki Kategoriler için ayarlayın.

ASP.NET Core çerçeve olayları için günlükleri yazar. Örneğin, için günlük çıktısını göz önünde bulundurun:

* RazorASP.NET Core şablonlarıyla oluşturulan bir sayfalar uygulaması.
* Günlük kayıt kümesi`Logging:Console:LogLevel:Microsoft:Information`
* Gizlilik sayfasına gidilme:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

Aşağıdaki JSON kümeleri `Logging:Console:LogLevel:Microsoft:Information` :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>Günlüğe olay KIMLIĞI

Her günlük bir *olay kimliği*belirtebilir. Örnek uygulama, `MyLogEvents` olay kimliklerini tanımlamak için sınıfını kullanır:

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Olay KIMLIĞI bir olay kümesini ilişkilendirir. Örneğin, bir sayfadaki öğelerin listesini görüntülemek için ilgili tüm Günlükler 1001 olabilir.

Günlüğe kaydetme sağlayıcısı, olay KIMLIĞINI günlüğe kaydetme iletisindeki kimlik alanında veya hiç değil, bir kimlik alanında saklayabilir. Hata ayıklama sağlayıcısı olay kimliklerini göstermiyor. Konsol sağlayıcısı, etkinlik kimliklerini kategoriden sonra parantez içinde gösterir:

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

Bazı günlük sağlayıcıları olay KIMLIĞINI bir alanda depolar, bu da KIMLIĞE filtrelemeye olanak tanır.

<a name="lmt"></a>

## <a name="log-message-template"></a>Günlük iletisi şablonu
<!-- Review, Each log API uses a message template. -->
Her günlük API 'SI bir ileti şablonu kullanır. İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucuları içerebilir. Sayılar değil, yer tutucular için adları kullanın.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Adlarının, değerlerinin sağlanması için hangi parametrelerin kullanılacağını belirleyen yer tutucular sırası. Aşağıdaki kodda, parametre adları ileti şablonunda sıra dışında:

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Yukarıdaki kod, sırasıyla parametre değerleriyle bir günlük iletisi oluşturur:

```text
Parameter values: param1, param2
```

Bu yaklaşım, günlük sağlayıcılarının [anlam veya yapılandırılmış günlük kaydı](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)uygulamasına olanak sağlar. Bağımsız değişkenler yalnızca biçimli ileti şablonuna değil, günlük sistemine geçirilir. Bu, günlük sağlayıcılarının parametre değerlerini alan olarak depolamasına olanak sağlar. Örneğin, aşağıdaki günlükçü yöntemini göz önünde bulundurun:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Örneğin, Azure Tablo depolama alanına oturum açarken:

* Her Azure Tablo varlığı `ID` ve özellikleri olabilir `RequestTime` .
* Özellikleri olan tablolar günlüğe kaydedilen verilerde sorguları basitleştirir. Örneğin, bir sorgu belirli bir aralıktaki tüm günlükleri, `RequestTime` kısa mesajdaki zaman aşımını ayrıştırmadan bulabilir.

## <a name="log-exceptions"></a>Günlük özel durumları

Günlükçü yöntemlerinde bir özel durum parametresi alan aşırı yüklemeleri var:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Özel durum günlüğe kaydetme, sağlayıcıya özgüdür.

### <a name="default-log-level"></a>Varsayılan günlük düzeyi

Varsayılan günlük düzeyi ayarlanmamışsa varsayılan günlük düzeyi değeri olur `Information` .

Örneğin, aşağıdaki Web uygulamasını göz önünde bulundurun:

* ASP.NET Web uygulaması şablonlarıyla oluşturulur.
* *appsettings.js* ve *appsettings.Development.js* silinmiş ya da yeniden adlandırıldı.

Önceki kurulumla, gizlilik veya giriş sayfasına gidildiğinde `Trace` `Debug` Kategori adında birçok, ve `Information` ile ileti üretilir `Microsoft` .

Aşağıdaki kod, varsayılan günlük düzeyi yapılandırmada ayarlanmamışsa varsayılan günlük düzeyini ayarlar:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
Genellikle, kod değil yapılandırmada günlük düzeyleri belirtilmelidir.

### <a name="filter-function"></a>Filter işlevi

Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

Önceki kod, kategori içerdiğinde `Controller` veya `Microsoft` ve günlük düzeyi `Information` veya daha yüksek olduğunda konsol günlüklerini görüntüler.

Genellikle, kod değil yapılandırmada günlük düzeyleri belirtilmelidir.

## <a name="aspnet-core-and-ef-core-categories"></a>ASP.NET Core ve EF Core kategorileri

Aşağıdaki tabloda, ASP.NET Core ve Entity Framework Core tarafından kullanılan ve Günlükler hakkındaki notlarla ilgili bazı kategoriler yer almaktadır:

| Kategori                            | Notlar |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Genel ASP.NET Core tanılama. |
| Microsoft.AspNetCore.DataProtection | Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı. |
| Microsoft.AspNetCore.HostFiltering  | İzin verilen konaklar. |
| Microsoft.AspNetCore.Hosting        | HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları. Hangi barındırma başlangıç derlemeleri yüklendi. |
| Microsoft.AspNetCore.Mvc            | MVC ve Razor Tanılama. Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi. |
| Microsoft.AspNetCore.Routing        | Eşleşen bilgileri yönlendirin. |
| Microsoft. AspNetCore. Server         | Bağlantı başlatın, durdurun ve canlı yanıtları koruyun. HTTPS sertifika bilgileri. |
| Microsoft.AspNetCore.StaticFiles    | Sunulan dosyalar. |
| Microsoft. EntityFrameworkCore       | Genel Entity Framework Core tanılama. Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler. |

Konsol penceresinde daha fazla kategori görüntülemek için **appsettings.Development.js** aşağıdaki şekilde ayarlayın:

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>Günlük kapsamları

 *Kapsam* bir mantıksal işlemler kümesini gruplandırabilir. Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir. Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.

Kapsam:

* <xref:System.IDisposable>, Yöntemi tarafından döndürülen türdür <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> .
* Atılana kadar sürer.

Aşağıdaki sağlayıcılar kapsamları destekler:

* `Console`
* [AzureAppServicesFile ve AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

Bir blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın `using` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

Aşağıdaki JSON konsol sağlayıcısı için kapsamları etkinleştirdi:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

Genellikle günlüğe kaydetme, kodda değil yapılandırmada belirtilmelidir.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>Yerleşik günlük oluşturma sağlayıcıları

ASP.NET Core aşağıdaki günlük sağlayıcılarını içerir:

* [Konsol](#console)
* [Hata ayıklama](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile ve AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

`stdout`ASP.NET Core modülüyle günlüğe kaydetme ve hata ayıklama ile ilgili bilgi için bkz <xref:test/troubleshoot-azure-iis> <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> . ve.

### <a name="console"></a>Konsol

`Console`Sağlayıcı çıktıyı konsola kaydeder. Geliştirme sırasında günlükleri görüntüleme hakkında daha fazla bilgi için `Console` bkz. [DotNet Run ve Visual Studio 'Dan çıktıyı günlüğe kaydetme](#dnrvs).

### <a name="debug"></a>Hata ayıklama

`Debug`Sağlayıcı, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını kullanarak günlük çıktısını yazar. `System.Diagnostics.Debug.WriteLine`Sağlayıcıya yazmak için çağrılar `Debug` .

Linux 'ta, `Debug` sağlayıcı günlük konumu dağıtıma bağımlıdır ve aşağıdakilerden biri olabilir:

* */var/log/Message*
* */var/log/Syslog*

### <a name="event-source"></a>Olay Kaynağı

`EventSource`Sağlayıcı, adlı platformlar arası bir olay kaynağına Yazar `Microsoft-Extensions-Logging` . Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.

#### <a name="dotnet-trace-tooling"></a>DotNet izleme araçları

[DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) Aracı, çalışan bir Işlemin .NET Core izlemelerinin toplanmasını sağlayan platformlar arası CLI genel aracıdır. Araç, <xref:Microsoft.Extensions.Logging.EventSource> kullanarak sağlayıcı verilerini toplar <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .

Bkz. yükleme yönergeleri için [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) .

Bir uygulamadan izleme toplamak için DotNet Trace araçları kullanın:

1. Komutuyla uygulamayı çalıştırın `dotnet run` .
1. .NET Core uygulamasının işlem tanımlayıcısını (PID) belirleme:
   * Windows 'ta aşağıdaki yaklaşımlardan birini kullanın:
     * Görev Yöneticisi (Ctrl + Alt + Del)
     * [Tasklist komutu](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process PowerShell komutu](/powershell/module/microsoft.powershell.management/get-process)
   * Linux 'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.

   Uygulamanın derlemesi ile aynı ada sahip olan işlem için PID 'i bulun.

1. Komutunu yürütün `dotnet trace` .

   Genel komut sözdizimi:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   PowerShell komut kabuğu kullanırken, `--providers` değeri tek tırnak işaretleri () içine alın `'` :

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Windows dışı platformlarda, `-f speedscope` Çıkış izleme dosyasının biçimini olarak değiştirme seçeneğini ekleyin `speedscope` .

   | Sözcükle | Açıklama |
   | :-----: | ----------- |
   | 1       | İçin meta olayları günlüğe kaydedin `LoggingEventSource` . Olayları günlüğe kaydetmez `ILogger` ). |
   | 2       | `Message`Çağrıldığında olayı açar `ILogger.Log()` . Programlı (biçimlendirilmedi) bir şekilde bilgi sağlar. |
   | 4       | `FormatMessage`Çağrıldığında olayı açar `ILogger.Log()` . , Bilgilerin biçimlendirilen dize sürümünü sağlar. |
   | 8       | `MessageJson`Çağrıldığında olayı açar `ILogger.Log()` . Bağımsız değişkenlerin JSON gösterimini sağlar. |

   | Olay düzeyi | Description     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`için girdiler `{Logger Category}` ve `{Event Level}` ek günlük filtreleme koşullarını temsil eder. `FilterSpecs`Girişleri noktalı virgül () ile ayırın `;` .

   Bir Windows komut kabuğu (değer etrafında tek tırnak**olmadan** ) ile örnek `--providers` :

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Yukarıdaki komut şunları etkinleştirir:

   * `4`Hatalar () için biçimlendirilen dizeler () üretmek üzere olay kaynağı günlükçüsü `2` .
   * `Microsoft.AspNetCore.Hosting`günlüğe kaydetme `Informational` düzeyinde () günlüğe kaydetme `4` .

1. ENTER tuşuna veya CTRL + C tuşlarına basarak DotNet izleme araçlarını durdurun.

   İzleme, komutun yürütüldüğü klasörde *Trace. NetTrace* adıyla kaydedilir `dotnet trace` .

1. Trace 'i [PerfView](#perfview)ile açın. *Trace. NetTrace* dosyasını açın ve izleme olaylarını araştırın.

Uygulama Konağı ile derlenmezse `CreateDefaultBuilder` , [olay kaynak sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.

Daha fazla bilgi için bkz.

* [Performans Analizi yardımcı programı Için izleme (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core belgeleri)
* [Performans Analizi yardımcı programı (DotNet-Trace) Için izleme](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (DotNet/Diagnostics GitHub deposu belgeleri)
* [Loggingeventsource sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API tarayıcısı)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Loggingeventsource başvuru kaynağı (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): farklı bir sürüm için başvuru kaynağı almak için dalı olarak değiştirin `release/{Version}` , burada `{Version}` ASP.NET Core sürümü istenen sürümdür.
* [PerfView](#perfview): olay kaynağı izlemelerini görüntülemek için yararlıdır.

#### <a name="perfview"></a>PerfView

Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın. ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.

Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin. `*`Dizenin başlangıcında kaçırmayın.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Windows olay günlüğü

`EventLog`Sağlayıcı, Windows olay günlüğüne günlük çıktısı gönderir. Diğer sağlayıcılardan farklı olarak, `EventLog` sağlayıcı varsayılan sağlayıcı ***not*** olmayan ayarları uygulamaz. `EventLog`Günlük ayarları belirtilmemişse, [Varsayılan olarak LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103)' ı kullanırlar.

Olayları daha düşük günlüğe kaydetmek için <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , günlük düzeyini açık olarak ayarlayın. Aşağıdaki örnek, olay günlüğü varsayılan günlük düzeyini şu şekilde ayarlar <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) geçiş yapabilir <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . `null`Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:

* `LogName`: "Uygulama"
* `SourceName`: ".NET çalışma zamanı"
* `MachineName`: Yerel makine adı kullanılır.

Aşağıdaki kod, ' `SourceName` nin varsayılan değerinden öğesini olarak değiştirir `".NET Runtime"` `MyLogs` :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure App Service

[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.

Sağlayıcı paketi, paylaşılan çerçeveye dahil değildir. Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.

Sağlayıcı ayarlarını yapılandırmak için <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> Aşağıdaki örnekte gösterildiği gibi ve kullanın:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Uygulama Azure App Service dağıtıldığında, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) bölümündeki ayarları kullanır. Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.

* **Uygulama günlüğü (dosya sistemi)**
* **Uygulama günlüğü (blob)**

Günlük dosyaları için varsayılan konum *D: \\ Home \\ LogFiles \\ uygulama* klasöründedir ve varsayılan dosya adı *diagnostics-yyyymmdd.txt*. Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir. Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid} -applicationLog.txt*.

Bu sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında günlüğe kaydedilir.

#### <a name="azure-log-streaming"></a>Azure günlük akışı

Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemeyi destekler:

* Uygulama sunucusu
* Web sunucusu
* Başarısız istek izleme

Azure günlük akışını yapılandırmak için:

* Uygulamanın Portal sayfasından **App Service günlükleri** sayfasına gidin.
* **Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.
* Günlük **düzeyini**seçin. Bu ayar yalnızca Azure günlük akışı için geçerlidir.

Günlükleri görüntülemek için **günlük akışı** sayfasına gidin. Günlüğe kaydedilen iletiler arabirimiyle günlüğe kaydedilir `ILogger` .

### <a name="azure-application-insights"></a>Azure Application Insights

[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri [Azure Application Insights](/azure/azure-monitor/app/cloudservices)yazar. Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir. Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.

Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir. Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.

[Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketi, ASP.NET Core değil, ASP.NET 4. x içindir.

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Application Insights'a genel bakış](/azure/application-insights/app-insights-overview)
* [ASP.NET Core uygulamalar için Application Insights](/azure/azure-monitor/app/asp-net-core) -günlük kaydı ile birlikte Application Insights telemetrinin tam aralığını uygulamak istiyorsanız buraya başlayın.
* [.NET Core ILogger günlükleri Için Applicationınsightsloggerprovider](/azure/azure-monitor/app/ilogger) -günlük sağlayıcısını Application Insights telemetri olmadan uygulamak istiyorsanız buraya başlayın.
* [Günlüğe kaydetme bağdaştırıcılarını Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* Microsoft Learn sitede Application Insights SDK-etkileşimli öğreticisini [yükleyip başlatın](/learn/modules/instrument-web-app-code-with-application-insights) .

## <a name="third-party-logging-providers"></a>Üçüncü taraf günlük oluşturma sağlayıcıları

ASP.NET Core ile birlikte çalışan üçüncü taraf günlük çerçeveleri:

* [ELMAH.io](https://elmah.io/) ([GitHub deposu](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub deposu](https://github.com/mattwcole/gelf-extensions-logging))
* [Jsnlog](https://jsnlog.com/) ([GitHub deposu](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub deposu](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub deposu](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub deposu](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub deposu](https://github.com/NLog/NLog.Extensions.Logging))
* [Pgünlükçü](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub deposu](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([GitHub deposu](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub deposu](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub deposu](https://github.com/googleapis/google-cloud-dotnet))

Bazı üçüncü taraf çerçeveler [, yapılandırılmış günlük olarak da bilinen anlam günlüğe kaydetme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)işlemini gerçekleştirebilir.

Bir üçüncü taraf çerçevesinin kullanılması, yerleşik sağlayıcılardan birini kullanmaya benzer:

1. Projenize bir NuGet paketi ekleyin.
1. `ILoggerFactory`Günlüğe kaydetme çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.

Daha fazla bilgi için bkz. her sağlayıcının belgeleri. Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>Konak olmayan konsol uygulaması

Genel konağın Web 'de olmayan bir uygulamada nasıl kullanılacağına ilişkin bir örnek için, [arka plan görevlerinin örnek uygulaması](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) () *program.cs* dosyasına bakın <xref:fundamentals/host/hosted-services> .

Genel ana bilgisayarı olmayan uygulamalar için günlük kodu, [sağlayıcıların Eklenme](#add-providers) ve [günlükçülerin oluşturulma](#create-logs)biçiminde farklılık gösterir. 

### <a name="logging-providers"></a>Günlüğe kaydetme sağlayıcıları

Konak olmayan bir konsol uygulamasında, `Add{provider name}` oluşturma sırasında sağlayıcının uzantı yöntemini çağırın `LoggerFactory` :

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>Günlükleri oluştur

Günlükler oluşturmak için bir nesnesi kullanın <xref:Microsoft.Extensions.Logging.ILogger%601> . `LoggerFactory`Oluşturmak için öğesini kullanın `ILogger` .

Aşağıdaki örnek `LoggingConsoleApp.Program` , kategorisi olarak bir günlükçü oluşturur.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

Aşağıdaki ASP.NET CORE örneklerinde, günlükçü, düzeyi olan Günlükler oluşturmak için kullanılır `Information` . Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

[Düzeyler](#log-level) ve [Kategoriler](#log-category) bu belgede daha ayrıntılı olarak açıklanmıştır.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>Konak oluşturma sırasında günlüğe kaydet

Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez. Ancak, ayrı bir günlükçü kullanılabilir. Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateHostBuilder` . `AddSerilog`, içinde belirtilen statik yapılandırmayı kullanır `Log.Logger` :

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>ILogger 'a bağlı bir hizmet yapılandırma

`Startup`Web ana bilgisayarı için ayrı bır dı kapsayıcısı oluşturulduğundan, bir günlükçü 'nin önceki ASP.NET Core sürümlerinde çalışacak şekilde bir günlükçü ekleme. Genel ana bilgisayar için yalnızca bir kapsayıcı oluşturma hakkında daha fazla bilgi için bkz. [Son değişiklik duyurusu](https://github.com/aspnet/Announcements/issues/353).

Bağımlı olan bir hizmeti yapılandırmak için `ILogger<T>` , Oluşturucu ekleme veya bir fabrika yöntemi sağlama kullanın. Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir. Örneğin, DI tarafından sağlanmış bir örneğe ihtiyacı olan bir hizmeti düşünün `ILogger<T>` :

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

Önceki vurgulanmış kod, ilk kez [Func](/dotnet/api/system.func-2) , bir örneği oluşturmak IÇIN, dı kapsayıcısının bir örneğini oluşturmak için gereken bir Func `MyService` . Kayıtlı hizmetlerden herhangi birine bu şekilde erişebilirsiniz.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Ana oturum oluşturma

Aşağıdaki kod, `Main` `ILogger` konak oluşturulduktan sonra bir örneğinden bir örnek alarak oturum açar:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Başlangıçta günlük oluşturma

Aşağıdaki kod günlükleri içinde yazar `Startup.Configure` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

Yöntemde dı kapsayıcı kurulumu tamamlanmadan önce Günlüklerin yazılması `Startup.ConfigureServices` desteklenmez:

* Oluşturucuya günlükçü ekleme `Startup` desteklenmiyor.
* Yöntem imzasına günlükçü ekleme `Startup.ConfigureServices` desteklenmiyor

Bu kısıtlamanın nedeni, günlük kaydının dı ve yapılandırmaya göre değişir ve bu da, ara ' ya bağlıdır. Dı kapsayıcısı bitene kadar ayarlanamaz `ConfigureServices` .

`ILogger<T>`Bir günlükçü 'nin daha önceki sürümlerde çalışma alanına bağlı olan veya neden Oluşturucu ekleme hakkında bilgi için `Startup` , bkz. [ILogger 'a bağımlı olan bir hizmeti yapılandırma](#csdi)

### <a name="no-asynchronous-logger-methods"></a>Zaman uyumsuz günlükçü yöntemi yok

Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır. Günlüğe kaydetme veri deposu yavaşsa doğrudan yazma. Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı ve sonra bunları yavaş depoya daha sonra taşımayı düşünün. Örneğin, SQL Server oturum açarken `Log` Yöntemler zaman uyumlu olduğundan doğrudan bir yöntemde yapmayın `Log` . Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin. Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>Çalışan bir uygulamadaki günlük düzeylerini değiştirme

Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez. Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir. Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider), günlük yapılandırmasını varsayılan olarak yeniden yükler. Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.

## <a name="ilogger-and-iloggerfactory"></a>ILogger ve ıloggerfactory

<xref:Microsoft.Extensions.Logging.ILogger%601>Ve <xref:Microsoft.Extensions.Logging.ILoggerFactory> arabirimleri ve uygulamaları .NET Core SDK dahil edilmiştir. Bunlar ayrıca aşağıdaki NuGet paketlerinde de kullanılabilir:  

* Arabirimler [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde bulunur.
* Varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)' dir.

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>Koddaki günlük filtresi kurallarını Uygula

Günlük filtresi kurallarını ayarlamak için tercih edilen yaklaşım [yapılandırma](xref:fundamentals/configuration/index)kullanmaktır.

Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)``System`Kategori ve günlük düzeyini belirtir `Debug` . Belirli bir sağlayıcı yapılandırılmadığı için filtre tüm sağlayıcılara uygulanır.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`belirtir

* `Debug`Günlüğe kaydetme sağlayıcısı.
* Günlük düzeyi `Information` ve üzeri.
* İle başlayan tüm kategoriler `"Microsoft"` .

## <a name="create-a-custom-logger"></a>Özel günlükçü oluşturma

Özel bir günlükçü eklemek için bir ile ekleyin <xref:Microsoft.Extensions.Logging.ILoggerProvider> <xref:Microsoft.Extensions.Logging.ILoggerFactory> :

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

, `ILoggerProvider` Bir veya daha fazla `ILogger` örnek oluşturur. `ILogger`Örnekler, bilgileri günlüğe kaydetmek için Framework tarafından kullanılır.

### <a name="sample-custom-logger-configuration"></a>Örnek özel günlükçü yapılandırması

Örnek:

* , Olay KIMLIĞI ve günlük düzeyi tarafından günlük konsolunun rengini ayarlayan çok basit bir örnek olacak şekilde tasarlanmıştır. Günlükçüler genellikle olay KIMLIĞINE göre değişmez ve günlük düzeyine özgü değildir.
* Aşağıdaki yapılandırma türünü kullanarak günlük düzeyi ve olay KIMLIĞI başına farklı renk konsolu girdileri oluşturur:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

Yukarıdaki kod, varsayılan düzeyi olarak `Warning` ' i ve rengini olarak ayarlar `Yellow` . `EventId`0 olarak ayarlanırsa tüm olayları günlüğe göndereceğiz.

### <a name="create-the-custom-logger"></a>Özel günlükçü oluşturma

`ILogger`Uygulama kategorisi adı genellikle günlük kaynağıdır. Örneğin, günlükçü 'nin oluşturulduğu tür:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

Yukarıdaki kod:

* Kategori adı başına bir günlükçü örneği oluşturur.
* İade `logLevel == _config.LogLevel` eder `IsEnabled` , her birinin `logLevel` benzersiz bir günlükçü vardır. Günlükçüler genellikle daha yüksek günlük seviyeleri için de etkinleştirilmelidir:

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>Özel LoggerProvider oluşturma

, `LoggerProvider` Günlükçü örneklerini oluşturan sınıftır. Kategori başına bir günlükçü örneği oluşturmak gerekli değildir, ancak bu, NLog veya Log4net gibi bazı Günlükçüler için mantıklı olur. Bunu yaptığınızda, gerekirse kategori başına farklı günlüğe kaydetme çıkış hedeflerini de seçebilirsiniz:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

Yukarıdaki kodda, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> Kategori başına adının tek bir örneğini oluşturur `ColorConsoleLogger` ve içinde depolar [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) .

### <a name="usage-and-registration-of-the-custom-logger"></a>Özel günlükçü kullanımı ve kaydı

Günlükçü kayıt defteri `Startup.Configure` :

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

Yukarıdaki kod için, için en az bir genişletme yöntemi sağlayın `ILoggerFactory` :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/loggermessage>
* Günlüğe kaydetme hataları [GitHub.com/DotNet/Runtime/](https://github.com/dotnet/runtime/issues) deposu 'nda oluşturulmalıdır.
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/) tarafından

.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler. Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Sağlayıcı Ekle

Günlük sağlayıcısı günlükleri görüntüler veya depolar. Örneğin, konsol sağlayıcısı günlükleri konsolunda görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Application Insights depolar. Birden çok sağlayıcı eklenerek Günlükler birden çok hedefe gönderilebilir.

Bir sağlayıcı eklemek için sağlayıcının `Add{provider name}` uzantı yöntemini *program.cs*içinde çağırın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Yukarıdaki kod, ve için başvuruları `Microsoft.Extensions.Logging` gerektirir `Microsoft.Extensions.Configuration` .

Varsayılan proje şablonu, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> aşağıdaki günlük sağlayıcılarını ekleyen çağırır:

* Konsol
* Hata ayıklama
* EventSource (ASP.NET Core 2,2 ' den başlayarak)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Kullanıyorsanız `CreateDefaultBuilder` , varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz. <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>Öğesini çağırın ve istediğiniz sağlayıcıları ekleyin.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Makalenin ilerleyen kısımlarında [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve [üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.

## <a name="create-logs"></a>Günlükleri oluştur

Günlükler oluşturmak için bir nesnesi kullanın <xref:Microsoft.Extensions.Logging.ILogger%601> . Bir Web uygulamasında veya barındırılan hizmette, bir `ILogger` bağımlılık ekleme (dı) kaynağından yararlanın. Konak dışı konsol uygulamalarında, oluşturmak için öğesini kullanın `LoggerFactory` `ILogger` .

Aşağıdaki ASP.NET Core örnek, kategorisi olarak içeren bir günlükçü oluşturur `TodoApiSample.Pages.AboutModel` . Günlük *kategorisi* , her günlük ile ilişkili bir dizedir. `ILogger<T>`Dı tarafından belirtilen örnek, kategori olarak tam nitelikli adı olan Günlükler oluşturur `T` . 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, günlükçü, düzeyi olan Günlükler oluşturmak için kullanılır `Information` . Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Düzeyler](#log-level) ve [Kategoriler](#log-category) Bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.

### <a name="create-logs-in-startup"></a>Başlangıçta günlük oluşturma

Sınıf içindeki günlükleri yazmak için `Startup` , `ILogger` Oluşturucu imzasına bir parametre ekleyin:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Program sınıfında Günlükler oluşturma

Sınıf içindeki günlükleri yazmak için `Program` , `ILogger` dı 'den bir örnek alın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez. Ancak, ayrı bir günlükçü kullanılabilir. Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateWebHostBuilder` . `AddSerilog`, içinde belirtilen statik yapılandırmayı kullanır `Log.Logger` :

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>Zaman uyumsuz günlükçü yöntemi yok

Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır. Günlüğe kaydetme veri depoluizin yavaşsa, doğrudan buna yazmayın. Başlangıç olarak günlük iletilerini hızlı bir mağazaya yazmayı ve sonra yavaş depoya daha sonra taşımayı düşünün. Örneğin, SQL Server için oturum açıyorsanız `Log` Yöntemler zaman uyumlu olduğundan, bunu doğrudan bir yöntemde yapmak istemezsiniz `Log` . Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin. Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.

## <a name="configuration"></a>Yapılandırma

Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:

* Dosya biçimleri (ıNı, JSON ve XML).
* Komut satırı bağımsız değişkenleri.
* Ortam değişkenleri.
* Bellek içi .NET nesneleri.
* Şifrelenmemiş [gizli dizi Yöneticisi](xref:security/app-secrets) depolaması.
* [Azure Key Vault](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.
* Özel sağlayıcılar (yüklü veya oluşturulmuş).

Örneğin, günlük yapılandırma genellikle `Logging` uygulama ayarları dosyalarının bölümü tarafından sağlanır. Aşağıdaki örnek, dosyasındaki tipik bir *appsettings.Development.js* içeriğini gösterir:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging`Özelliği, `LogLevel` ve günlük sağlayıcısı özelliklerine sahip olabilir (konsol gösterilir).

`LogLevel`Altındaki özelliği, `Logging` Seçili kategoriler için günlüğe kaydedilecek minimum [düzeyi](#log-level) belirtir. Örnekte `System` ve `Microsoft` Kategoriler düzeyinde günlüğe kaydedilir `Information` ve diğer tüm diğerleri düzeyinde günlüğe kaydedilir `Debug` .

`Logging`Günlük sağlayıcılarını belirt altındaki diğer özellikler. Örnek, konsol sağlayıcısına yöneliktir. Bir sağlayıcı, [günlük kapsamlarını](#log-scopes)destekliyorsa, etkinleştirilip etkinleştirilmeyeceğini `IncludeScopes` belirtir. Bir sağlayıcı özelliği (örnekte olduğu gibi `Console` ), bir özellik de belirtebilir `LogLevel` . `LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir.

' De düzeyler belirtilirse `Logging.{providername}.LogLevel` , içinde ayarlanan her şeyi geçersiz kılar `Logging.LogLevel` . Örneğin, aşağıdaki JSON 'u göz önünde bulundurun:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

Önceki JSON 'da, `Console` sağlayıcı ayarları önceki (varsayılan) günlük düzeyini geçersiz kılar.

Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez. Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir. Örneğin, ayar dosyalarını okumak için tarafından eklenen [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) `CreateDefaultBuilder` , günlük yapılandırmasını varsayılan olarak yeniden yükler. Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.

Yapılandırma sağlayıcılarını uygulama hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..

## <a name="sample-logging-output"></a>Örnek günlüğe kaydetme çıkışı

Yukarıdaki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında Günlükler konsolunda görüntülenir. Konsol çıkışının bir örneği aşağıda verilmiştir:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

Yukarıdaki Günlükler, üzerinde örnek uygulamaya bir HTTP GET isteği yapılarak oluşturulmuştur `http://localhost:5000/api/todo/0` .

Visual Studio 'da örnek uygulamayı çalıştırdığınızda hata ayıklama penceresinde göründükleri günlüklere yönelik bir örnek aşağıda verilmiştir:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

`ILogger`Yukarıdaki bölümde gösterilen çağrılar tarafından oluşturulan Günlükler "TodoApi" ile başlar. "Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır. ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.

Bu makalenin geri kalanında günlüğe kaydetme için bazı ayrıntılar ve seçenekler açıklanmaktadır.

## <a name="nuget-packages"></a>NuGet paketleri

`ILogger`Ve `ILoggerFactory` arabirimleri [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde ve bu uygulamalar için varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)içinde bulunur.

## <a name="log-category"></a>Günlük kategorisi

Bir `ILogger` nesne oluşturulduğunda, için bir *Kategori* belirtilir. Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger` . Kategori herhangi bir dize olabilir, ancak kural, "TodoApi. Controllers. TodoController" gibi sınıf adını kullanmaktır.

`ILogger<T>` `ILogger` Kategori olarak tam nitelikli tür adını kullanan bir örnek almak için kullanın `T` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`, `CreateLogger` tam nitelikli tür adı ile çağırma ile eşdeğerdir `T` .

## <a name="log-level"></a>Günlük düzeyi

Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir. Günlük düzeyi önem derecesini veya önemini gösterir. Örneğin, bir yöntem `Information` normal olarak sona erdiğinde ve bir `Warning` Yöntem *404* bulunmayan bir durum kodu döndürdüğünde bir günlük yazabilirsiniz.

Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Yukarıdaki kodda, `MyLogEvents.GetItem` ve `MyLogEvents.GetItemNotFound` parametreleri [günlük olay kimliğidir](#log-event-id). İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur. Yöntem parametreleri bu makaledeki [günlük iletisi şablonu bölümünde](#lmt) açıklanmaktadır.

Yöntem adında düzeyi (örneğin, ve) içeren günlük yöntemleri, `LogInformation` `LogWarning` [ILogger için uzantı yöntemleridir](xref:Microsoft.Extensions.Logging.LoggerExtensions). Bu yöntemler bir `Log` parametre alan bir yöntemi çağırır `LogLevel` . `Log`Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak söz dizimi görece karmaşıktır. Daha fazla bilgi için bkz <xref:Microsoft.Extensions.Logging.ILogger> . ve [günlükçü uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core, en küçükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.

* İzleme = 0

  Genellikle yalnızca hata ayıklama için değerli bilgiler için. Bu iletiler hassas uygulama verileri içerebilir, bu nedenle bir üretim ortamında etkinleştirilmemelidir. *Varsayılan olarak devre dışıdır.*

* Hata Ayıkla = 1

  Geliştirme ve hata ayıklama konusunda yararlı olabilecek bilgiler için. Örnek: `Entering method Configure with flag set to true.` `Debug` yüksek günlük hacimden dolayı yalnızca sorun giderirken üretim ortamında düzey günlüklerini etkinleştirin.

* Bilgi = 2

  Uygulamanın genel akışını izlemek için. Bu günlüklerde genellikle uzun süreli bir değer vardır. Örnek: `Request received for path /api/todo`

* Uyarı = 3

  Uygulama akışında anormal veya beklenmedik olaylar için. Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gerekebilecek hataları veya diğer koşulları içerebilir. İşlenmiş özel durumlar, günlük düzeyini kullanmak için yaygın bir yerdir `Warning` . Örnek: `FileNotFoundException for file quotes.txt.`

* Hata = 4

  İşlenemeyen hatalar ve özel durumlar için. Bu iletiler, uygulama genelinde bir hata değil geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir. Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`

* Kritik = 5

  Anında ilgilenilmesi gereken hatalarda. Örnekler: veri kaybı senaryoları, disk alanı yetersiz.

Belirli bir depolama ortamında veya görüntüleme penceresinde ne kadar günlük çıkışının yazıldığını denetlemek için günlük düzeyini kullanın. Örneğin:

* Üretimde:
  * Geçiş düzeyinde günlüğe kaydetme, `Trace` `Information` yüksek hacimli ayrıntılı günlük iletileri oluşturur. Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, `Trace` `Information` düzey iletiler üzerinden yüksek hacimli, düşük maliyetli bir veri deposuna oturum açın.
  * Düzeylerde oturum açma işlemi `Warning` `Critical` genellikle daha az, daha küçük günlük iletileri üretir. Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir ve bu da veri deposu seçiminden daha fazla esneklik elde etmez.
* Geliştirme sırasında:
  * `Warning`Konsola iletiler aracılığıyla oturum açın `Critical` .
  * `Trace` `Information` Sorun giderirken iletiler aracılığıyla ekleyin.

Bu makalede daha sonra bulunan [günlük filtreleme](#log-filtering) bölümünde, bir sağlayıcının hangi günlük düzeylerinin işlediğini nasıl denetleneceği açıklanmaktadır.

ASP.NET Core çerçeve olayları için günlükleri yazar. Bu makalenin önceki kısımlarında yer alınan günlük örnekleri, günlüklerin altında tutulur `Information` , bu nedenle hiçbir `Debug` veya `Trace` düzeyi günlük oluşturulmaz. Günlükleri göstermek için yapılandırılmış örnek uygulama çalıştırılarak oluşturulan konsol günlüklerinin bir örneği aşağıda verilmiştir `Debug` :

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>Günlüğe olay KIMLIĞI

Her günlük bir *olay kimliği*belirtebilir. Örnek uygulama bunu yerel olarak tanımlanmış bir sınıf kullanarak yapar `LoggingEvents` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Olay KIMLIĞI bir olay kümesini ilişkilendirir. Örneğin, bir sayfadaki öğelerin listesini görüntülemek için ilgili tüm Günlükler 1001 olabilir.

Günlüğe kaydetme sağlayıcısı, olay KIMLIĞINI günlüğe kaydetme iletisindeki kimlik alanında veya hiç değil, bir kimlik alanında saklayabilir. Hata ayıklama sağlayıcısı olay kimliklerini göstermiyor. Konsol sağlayıcısı, etkinlik kimliklerini kategoriden sonra parantez içinde gösterir:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Günlük iletisi şablonu

Her günlük bir ileti şablonunu belirtir. İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucuları içerebilir. Sayılar değil, yer tutucular için adları kullanın.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Adlarının, değerlerinin sağlanması için hangi parametrelerin kullanılacağını belirleyen yer tutucular sırası. Aşağıdaki kodda, parametre adlarının ileti şablonunda sıra dışı olduğuna dikkat edin:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Bu kod, sırasıyla parametre değerleriyle bir günlük iletisi oluşturur:

```text
Parameter values: parm1, parm2
```

Günlüğe kaydetme altyapısı bu şekilde çalışarak, günlük sağlayıcılarının [yapılandırılmış günlüğe yazma olarak da bilinen anlamsal günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulayabilmesini sağlayabilir. Bağımsız değişkenler yalnızca biçimli ileti şablonuna değil, günlük sistemine geçirilir. Bu bilgiler, günlük sağlayıcılarının parametre değerlerini alan olarak depolamasına olanak sağlar. Örneğin, günlükçü yönteminin şuna benzer şekilde göründüğünü varsayın:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Günlükleri Azure Tablo depolama alanına gönderiyorsanız, her bir Azure Tablo varlığı, `ID` `RequestTime` günlük verilerinde sorguları basitleştiren ve özelliklere sahip olabilir. Bir sorgu, belirli bir aralıktaki tüm günlükleri, `RequestTime` Kısa mesajdan zaman aşımını ayrıştırmadan bulabilir.

## <a name="logging-exceptions"></a>Günlüğe kaydetme özel durumları

Günlükçü yöntemlerinin, aşağıdaki örnekte olduğu gibi bir özel durum iletmenizi sağlayan aşırı yüklemeleri vardır:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Özel durum bilgileri farklı yollarla farklı sağlayıcılarda işler. Yukarıda gösterilen koddan hata ayıklama sağlayıcısı çıktısına bir örnek aşağıda verilmiştir.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Günlük filtreleme

Belirli bir sağlayıcı ve kategori için en az bir günlük düzeyi veya tüm sağlayıcılar ya da tüm kategoriler için belirtebilirsiniz. Minimum düzeyin altındaki tüm Günlükler bu sağlayıcıya aktarılmaz, bu nedenle görüntülenmez veya depolanmaz.

Tüm günlükleri gizlemek için `LogLevel.None` En düşük günlük düzeyi olarak belirtin. Öğesinin tamsayı değeri `LogLevel.None` 6 ' dır `LogLevel.Critical` (5) daha yüksektir.

### <a name="create-filter-rules-in-configuration"></a>Yapılandırmada filtre kuralları oluşturma

Proje şablonu kodu, `CreateDefaultBuilder` konsol, hata ayıklama ve EventSource (ASP.NET Core 2,2 veya üzeri) sağlayıcılar için günlük kaydı ayarlamak için çağırır. `CreateDefaultBuilder`Yöntemi `Logging` , [Bu makalenin önceki kısımlarında](#configuration)açıklandığı gibi, bir bölümde yapılandırmayı aramak için günlük kaydı yapar.

Yapılandırma verileri aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en düşük günlük düzeylerini belirtir:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Bu JSON altı filtre kuralı oluşturur: biri hata ayıklama sağlayıcısı, konsol sağlayıcısı için dört ve diğeri tüm sağlayıcılar için. Bir nesne oluşturulduğunda her sağlayıcı için tek bir kural seçilir `ILogger` .

### <a name="filter-rules-in-code"></a>Koddaki filtre kuralları

Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

İkincisi, `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir. Birincisi `AddFilter` bir sağlayıcı türü belirtmediğinden, tüm sağlayıcılar için geçerlidir.

### <a name="how-filtering-rules-are-applied"></a>Filtreleme kuralları nasıl uygulanır

Yapılandırma verileri ve `AddFilter` Önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur. İlk altı yapılandırma örneğinde ve son iki ise kod örneğinde gelir.

| Sayı | Sağlayıcı      | Şununla başlayan Kategoriler...          | En düşük günlük düzeyi |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Hata ayıklama         | Tüm kategoriler                          | Bilgi       |
| 2      | Konsol       | Microsoft. AspNetCore. Mvc. Razor . İç | Uyarı           |
| 3      | Konsol       | Microsoft. AspNetCore. Mvc. Razor .Razor    | Hata ayıklama             |
| 4      | Konsol       | Microsoft. AspNetCore. Mvc.Razor          | Hata             |
| 5      | Konsol       | Tüm kategoriler                          | Bilgi       |
| 6      | Tüm sağlayıcılar | Tüm kategoriler                          | Hata ayıklama             |
| 7      | Tüm sağlayıcılar | Sistem                                  | Hata ayıklama             |
| 8      | Hata ayıklama         | Microsoft                               | İzleme             |

Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer. Bir örnek tarafından yazılan tüm iletiler `ILogger` , seçilen kurallara göre filtrelenmiştir. Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.

Belirli bir kategori için oluşturulan her sağlayıcı için aşağıdaki algoritma kullanılır `ILogger` :

* Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin. Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.
* Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin. Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.
* Birden çok kural seçilirse, **son** olanı götürün.
* Hiçbir kural seçilmezse, kullanın `MinimumLevel` .

Yukarıdaki kurallarla ilgili olarak, `ILogger` "Microsoft. AspNetCore. Mvc." adlı kategori için bir nesne oluşturduğunuzu varsayalım. Razor Razor ViewEngine ":

* Hata ayıklama sağlayıcısı, kurallar 1, 6 ve 8 için geçerlidir. Kural 8 ' i en özeldir, yani seçili olanı seçilidir.
* Konsol sağlayıcısı için, kurallar 3, 4, 5 ve 6 geçerlidir. Kural 3 en özeldir.

Elde edilen `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzeyi ve üzeri Günlükler gönderir. `Debug`Düzeyi ve üzeri Günlükler konsol sağlayıcısına gönderilir.

### <a name="provider-aliases"></a>Sağlayıcı diğer adları

Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *diğer ad* tanımlar.  Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:

* Konsol
* Hata ayıklama
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Varsayılan en düşük düzey

Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kural uygulanmaz geçerli olan en düşük düzey ayar vardır. Aşağıdaki örnekte, en düşük düzeyin nasıl ayarlanacağı gösterilmektedir:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

En düşük düzeyi açıkça ayarlamazsanız, varsayılan değer olur `Information` ve bu, `Trace` `Debug` günlüklerin yok sayılacağı anlamına gelir.

### <a name="filter-functions"></a>Filtre işlevleri

Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağırılır. İşlevindeki kodun sağlayıcı türü, kategorisi ve günlük düzeyine erişimi vardır. Örneğin:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Sistem kategorileri ve Düzeyler

ASP.NET Core ve Entity Framework Core tarafından kullanılan bazı kategoriler şunlardır ve bunlardan beklenen Günlükler hakkında notlar bulunur:

| Kategori                            | Notlar |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Genel ASP.NET Core tanılama. |
| Microsoft.AspNetCore.DataProtection | Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı. |
| Microsoft.AspNetCore.HostFiltering  | İzin verilen konaklar. |
| Microsoft.AspNetCore.Hosting        | HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları. Hangi barındırma başlangıç derlemeleri yüklendi. |
| Microsoft.AspNetCore.Mvc            | MVC ve Razor Tanılama. Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi. |
| Microsoft.AspNetCore.Routing        | Eşleşen bilgileri yönlendirin. |
| Microsoft. AspNetCore. Server         | Bağlantı başlatın, durdurun ve canlı yanıtları koruyun. HTTPS sertifika bilgileri. |
| Microsoft.AspNetCore.StaticFiles    | Sunulan dosyalar. |
| Microsoft. EntityFrameworkCore       | Genel Entity Framework Core tanılama. Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler. |

## <a name="log-scopes"></a>Günlük kapsamları

 *Kapsam* bir mantıksal işlemler kümesini gruplandırabilir. Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir. Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.

Kapsam `IDisposable` , yöntemi tarafından döndürülen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> ve atılana kadar sürer olan bir türdür. Bir blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın `using` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> `IncludeScopes`Kapsam tabanlı günlüğe kaydetmeyi etkinleştirmek için konsol günlükçü seçeneğinin yapılandırılması gerekir.
>
> Yapılandırma hakkında bilgi için [yapılandırma](#configuration) bölümüne bakın.

Her günlük iletisi kapsamlı bilgiler içerir:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Yerleşik günlük oluşturma sağlayıcıları

ASP.NET Core aşağıdaki sağlayıcıları sevk eder:

* [Konsol](#console-provider)
* [Hata ayıklama](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Stdout ve ASP.NET Core modüllü hata ayıklama günlüğü hakkında bilgi için bkz <xref:test/troubleshoot-azure-iis> . ve <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .

### <a name="console-provider"></a>Konsol sağlayıcısı

[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi, günlük çıktısını konsola gönderir. 

```csharp
logging.AddConsole();
```

Konsol günlüğü çıkışını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Hata ayıklama sağlayıcısı

[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını (Yöntem çağrıları) kullanarak günlük çıktısını yazar `Debug.WriteLine` .

Linux 'ta, bu sağlayıcı günlükleri */var/log/Message*dosyasına yazar.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Olay kaynak sağlayıcısı

[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi, adıyla bir olay kaynağı çapraz platformuna yazar `Microsoft-Extensions-Logging` . Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.

```csharp
logging.AddEventSourceLogger();
```

Olay kaynak sağlayıcısı, `CreateDefaultBuilder` konağı oluşturmak için çağrıldığında otomatik olarak eklenir.

Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın. ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.

Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin. (Dizenin başlangıcında yıldız işaretini kaçırmayın.)

![PerfView ek sağlayıcıları](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows olay günlüğü sağlayıcısı

[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi, Windows olay günlüğüne günlük çıktısı gönderir.

```csharp
logging.AddEventLog();
```

[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) , geçiş yapmanızı sağlar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . `null`Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:

* `LogName`: "Uygulama"
* `SourceName`: ".NET çalışma zamanı"
* `MachineName`: Yerel makine adı kullanılır.

Olaylar, [Uyarı düzeyi ve üzeri](#log-level)için günlüğe kaydedilir. Aşağıdaki örnek, olay günlüğü varsayılan günlük düzeyini şu şekilde ayarlar <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>TraceSource sağlayıcısı

[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıklarını ve sağlayıcıları kullanır.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Addtracesource aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) , bir kaynak anahtarı ve bir izleme dinleyicisi geçirmenize olanak sağlar.

Bu sağlayıcıyı kullanmak için, bir uygulamanın .NET Framework çalışması gerekir (.NET Core yerine). Sağlayıcı, iletileri örnek uygulamada kullanılan gibi çeşitli [dinleyicilerine](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir <xref:System.Diagnostics.TextWriterTraceListener> .

### <a name="azure-app-service-provider"></a>Azure App Service sağlayıcı

[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Sağlayıcı paketi [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahil değildir. .NET Framework veya `Microsoft.AspNetCore.App` metapackage 'e başvuru yaparken, sağlayıcı paketini projeye ekleyin. 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>Aşırı yükleme, geçiş yapmanızı sağlar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> . Ayarlar nesnesi, günlük çıkış şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir. (*Çıktı şablonu* , bir yöntem çağrısıyla birlikte sağlandığının yanı sıra tüm günlüklere uygulanan bir ileti şablonudur `ILogger` .)

App Service uygulamasına dağıtırken, uygulama, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarları kabul eder. Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.

* **Uygulama günlüğü (dosya sistemi)**
* **Uygulama günlüğü (blob)**

Günlük dosyaları için varsayılan konum *D: \\ Home \\ LogFiles \\ uygulama* klasöründedir ve varsayılan dosya adı *diagnostics-yyyymmdd.txt*. Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir. Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid} -applicationLog.txt*.

Sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında çalışır. Proje yerel olarak çalıştırıldığında, &mdash; yerel dosyalara veya blob 'lar için yerel geliştirme depolamasına yazmazsa, bu, hiçbir etkiye sahip değildir.

#### <a name="azure-log-streaming"></a>Azure günlük akışı

Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemenize izin verir:

* Uygulama sunucusu
* Web sunucusu
* Başarısız istek izleme

Azure günlük akışını yapılandırmak için:

* Uygulamanızın Portal sayfasından **App Service günlükleri** sayfasına gidin.
* **Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.
* Günlük **düzeyini**seçin. Bu ayar, uygulamadaki diğer günlük sağlayıcılarını değil, yalnızca Azure günlük akışı için geçerlidir.

Uygulama iletilerini görüntülemek için **günlük akışı** sayfasına gidin. Uygulama tarafından arabirim aracılığıyla günlüğe kaydedilir `ILogger` .

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights izleme günlüğü

[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri Azure Application Insights yazar. Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir. Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.

Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir. Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.

ASP.NET 4. x için [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini kullanmayın &mdash; .

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Application Insights'a genel bakış](/azure/application-insights/app-insights-overview)
* [ASP.NET Core uygulamalar için Application Insights](/azure/azure-monitor/app/asp-net-core) -günlük kaydı ile birlikte Application Insights telemetrinin tam aralığını uygulamak istiyorsanız buraya başlayın.
* [.NET Core ILogger günlükleri Için Applicationınsightsloggerprovider](/azure/azure-monitor/app/ilogger) -günlük sağlayıcısını Application Insights telemetri olmadan uygulamak istiyorsanız buraya başlayın.
* [Günlüğe kaydetme bağdaştırıcılarını Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* Microsoft Learn sitede Application Insights SDK-etkileşimli öğreticisini [yükleyip başlatın](/learn/modules/instrument-web-app-code-with-application-insights) .

## <a name="third-party-logging-providers"></a>Üçüncü taraf günlük oluşturma sağlayıcıları

ASP.NET Core ile birlikte çalışan üçüncü taraf günlük çerçeveleri:

* [ELMAH.io](https://elmah.io/) ([GitHub deposu](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub deposu](https://github.com/mattwcole/gelf-extensions-logging))
* [Jsnlog](https://jsnlog.com/) ([GitHub deposu](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub deposu](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub deposu](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub deposu](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub deposu](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([GitHub deposu](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub deposu](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub deposu](https://github.com/googleapis/google-cloud-dotnet))

Bazı üçüncü taraf çerçeveler [, yapılandırılmış günlük olarak da bilinen anlam günlüğe kaydetme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)işlemini gerçekleştirebilir.

Bir üçüncü taraf çerçevesinin kullanılması, yerleşik sağlayıcılardan birini kullanmaya benzer:

1. Projenize bir NuGet paketi ekleyin.
1. `ILoggerFactory`Günlüğe kaydetme çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.

Daha fazla bilgi için bkz. her sağlayıcının belgeleri. Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
