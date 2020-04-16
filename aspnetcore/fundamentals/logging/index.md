---
title: .NET Core ve ASP.NET Core'da Oturum Açma
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet paketi tarafından sağlanan günlük çerçevesini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440746"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core ve ASP.NET Core'da Oturum Açma

::: moniker range=">= aspnetcore-3.0"

Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/)

.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler. Bu makalede, yerleşik sağlayıcılarla günlük API'sinin nasıl kullanılacağı gösterilmektedir.

Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalarındandır. Bu kod parçacıklarının günlüğe özgü [bölümleri, Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanan herhangi bir .NET Core uygulaması için geçerlidir. Web konsolu olmayan bir uygulamada Genel Ana Bilgisayar'ın nasıl kullanılacağına bir örnek olarak, [Arka Plan Görevleri örnek uygulamasının](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) <xref:fundamentals/host/hosted-services> *Program.cs* dosyasına bakın .

Genel Ana Bilgisayar olmayan uygulamaların günlük [kodu, sağlayıcıların eklenme](#add-providers) ve [kaydedicilerin oluşturulma](#create-logs)şeklinde farklılık gösterir. Ana bilgisayar kodu örnekleri makalenin bu bölümlerinde gösterilmiştir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Sağlayıcılar ekleme

Bir günlük sağlayıcısı günlükleri görüntüler veya depolar. Örneğin, Konsol sağlayıcısı konsolda günlükleri görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Uygulama Öngörüleri'nde depolar. Günlükler, birden çok sağlayıcı eklenerek birden çok hedefe gönderilebilir.

Genel Ana Bilgisayar kullanan bir uygulamaya sağlayıcı eklemek `Add{provider name}` *için, sağlayıcının*uzantı yöntemini Program.cs:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

Barındırılamayan bir konsol uygulamasında, `Add{provider name}` sağlayıcının uzatma `LoggerFactory`yöntemini arayarak aşağıdakileri

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory`ve `AddConsole` için `using` `Microsoft.Extensions.Logging`bir ifade gerektirir.

Varsayılan ASP.NET Core proje <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>şablonları çağrı , aşağıdaki günlük sağlayıcıları ekler:

* [Konsol](#console-provider)
* [Hata ayıklama](#debug-provider)
* [Olaykaynağı](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (yalnızca Windows'da çalışırken)

Varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz. Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

Makalenin ilerleyen saatlerinde [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve üçüncü taraf günlük [sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.

## <a name="create-logs"></a>Günlükler oluşturma

Günlükleri oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> bir nesne kullanın. Bir web uygulamasında veya barındırılan bir hizmette, bağımlılık enjeksiyonundan (DI) bir `ILogger` uygulama alın. Ana bilgisayar olmayan konsol uygulamalarında, `LoggerFactory` `ILogger`bir .

Aşağıdaki ASP.NET Core örneği, kategori `TodoApiSample.Pages.AboutModel` olarak bir logger oluşturur. Günlük *kategorisi,* her günlükle ilişkili bir dizedir. DI `ILogger<T>` tarafından sağlanan örnek, kategori olarak tam nitelikli `T` türü ada sahip günlükleri oluşturur. 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Aşağıdaki ana bilgisayar olmayan konsol uygulaması örneği, `LoggingConsoleApp.Program` kategori olarak bir logger oluşturur.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, logger düzeyi `Information` olarak günlükleri oluşturmak için kullanılır. Günlük *düzeyi,* günlüğe kaydedilen olayın önem derecesini gösterir.

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

[Düzeyleri](#log-level) ve [kategorileri](#log-category) daha ayrıntılı olarak bu makalede açıklanmıştır.

### <a name="create-logs-in-the-program-class"></a>Program sınıfında günlük oluşturma

ASP.NET Core `Program` uygulamasının sınıfına günlük yazmak için, ana bilgisayarı yaptıktan sonra DI'den bir `ILogger` örnek alın:

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez. Ancak, ayrı bir logger kullanılabilir. Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateHostBuilder`açmak için kullanılır. `AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:

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

### <a name="create-logs-in-the-startup-class"></a>Başlangıç sınıfında günlük oluşturma

ASP.NET Core uygulaması `Startup.Configure` nın yönteminde günlük yazmak `ILogger` için yöntem imzasına bir parametre ekleyin:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

`Startup.ConfigureServices` Yöntemdeki DI kapsayıcı kurulumu tamamlanmadan önce günlükleri yazma desteklenmez:

* `Startup` Logger enjeksiyon yapıcı içine desteklenmez.
* Yöntem imzasına `Startup.ConfigureServices` logger enjeksiyonu desteklenmez

Bu kısıtlamanın nedeni, günlüğe kaydetmenin DI'ye ve yapılandırmaya bağlı olmasıdır, bu da sırayla DI'ye bağlıdır. DI kapsayıcısı bitene kadar `ConfigureServices` kurulmadı.

Web Barındırıcısı için `Startup` ayrı bir DI kapsayıcı oluşturulduğundan, ASP.NET Core'un önceki sürümlerinde bir logger'ın oluşturucu enjeksiyonu. Genel Ana Bilgisayar için neden yalnızca bir kapsayıcı oluşturulduğu hakkında bilgi [için, kesme değişikliği duyurusuna](https://github.com/aspnet/Announcements/issues/353)bakın.

Bağlı bir hizmeti yapılandırmanız `ILogger<T>`gerekiyorsa, bunu yine de yapıcı enjeksiyon kullanarak veya bir fabrika yöntemi sağlayarak yapabilirsiniz. Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir. Örneğin, bir mülkü DI'den bir hizmetle doldurmanız gerektiğini varsayalım:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Önceki vurgulanan kod, `Func` DI kapsayıcısının bir örneğini oluşturmak için `MyService`ilk kez çalışan bir koddur. Kayıtlı hizmetlerden herhangi biri bu şekilde erişebilirsiniz.

### <a name="no-asynchronous-logger-methods"></a>Asynchronous logger yöntemleri yok

Günlüğe kaydetme, eşzamanlı kodun performans maliyetine değmeyecek kadar hızlı olmalıdır. Günlük veri deponuz yavaşsa, doğrudan yazmayın. Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı düşünün, ardından yavaş mağazaya taşıyın. Örneğin, SQL Server'a günlüğe kaydoluyorsanız, yöntemler eşzamanlı olduğundan `Log` bunu doğrudan `Log` bir yöntemle yapmak istemezsiniz. Bunun yerine, eşzamanlı olarak bellek içi sıraya günlük iletileri ekleyin ve bir arka plan çalışanı sql server veri itme asynchronous iş yapmak için sıranın dışına iletileri çekin. Daha fazla bilgi için [bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.

## <a name="configuration"></a>Yapılandırma

Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:

* Dosya biçimleri (INI, JSON ve XML).
* Komut satırı bağımsız değişkenleri.
* Ortam değişkenleri.
* Bellek içi .NET nesneleri.
* Şifrelenmemiş [Gizli Yönetici](xref:security/app-secrets) depolama.
* [Azure Anahtar Kasası](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.
* Özel sağlayıcılar (yüklü veya oluşturulmuş).

Örneğin, günlüğe kaydetme yapılandırması `Logging` genellikle uygulama ayarları dosyaları bölümü tarafından sağlanır. Aşağıdaki örnekte, tipik bir uygulamanın içeriği *gösterilmektedir. Development.json* dosyası:

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

Özellik `Logging` olabilir `LogLevel` ve günlük sağlayıcı özellikleri (Konsol gösterilir).

Aşağıdaki `LogLevel` `Logging` özellik, seçili kategoriler için günlüğe kaydolacak minimum [düzey](#log-level) belirtir. Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe, diğer `Debug` tüm düzeylerde günlüğe kaydedin.

Altında `Logging` diğer özellikler günlük sağlayıcıları belirtir. Örnek Konsol sağlayıcısı içindir. Bir sağlayıcı [günlük kapsamlarını destekliyorsa,](#log-scopes) `IncludeScopes` etkin olup olmadıklarını gösterir. Bir sağlayıcı özelliği `Console` (örneğin gibi) da `LogLevel` bir özellik belirtebilir. `LogLevel`bir sağlayıcı altında bu sağlayıcı için oturum açmak için düzeyleri belirtir.

Düzeyler belirtilirse, `Logging.{providername}.LogLevel`'de `Logging.LogLevel`ayarlanan bir şeyi geçersiz kılarlar.

Günlük API'si, bir uygulama çalışırken günlük düzeylerini değiştirmek için bir senaryo içermez. Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu da günlüğe kaydetme yapılandırması üzerinde hemen etkili olur. Örneğin, ayarlar dosyalarını okumak `CreateDefaultBuilder` için eklenen Dosya Yapılandırma [Sağlayıcısı,](xref:fundamentals/configuration/index#file-configuration-provider)varsayılan olarak günlük yapılandırmasını yeniden yükler. Bir uygulama çalışırken yapılandırma kod olarak değiştirilirse, uygulama uygulamanın günlük yapılandırmasını güncellemek için [IConfigurationRoot.Reload'ı](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) arayabilir.

Yapılandırma sağlayıcılarının uygulanması hakkında <xref:fundamentals/configuration/index>bilgi için bkz.

## <a name="sample-logging-output"></a>Örnek günlük çıktısı

Önceki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında günlükler konsolda görünür. Konsol çıkışına bir örnek aşağıda verilmiştir:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

Önceki günlükler, `http://localhost:5000/api/todo/0`örnek uygulamaya http get isteği yaparak oluşturuldu.

Örnek uygulamayı Visual Studio'da çalıştırdığınızda Hata Ayıklama penceresinde görünen günlüklerin bir örneği aşağıda verilmiştir:

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApiSample" ile başlar. "Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir. ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.

Bu makalenin geri kalanı, günlüğe kaydetme için bazı ayrıntıları ve seçenekleri açıklar.

## <a name="nuget-packages"></a>NuGet paketleri

Ve arabirimler [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve onlar için varsayılan uygulamalar [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)bulunmaktadır. `ILoggerFactory` `ILogger`

## <a name="log-category"></a>Günlük kategorisi

Bir `ILogger` nesne oluşturulduğunda, bunun için bir *kategori* belirtilir. Bu kategori, bu örnek tarafından oluşturulan her `ILogger`günlük iletisi ile birlikte verilir. Kategori herhangi bir dize olabilir, ancak kural "TodoApi.Controllers.TodoController" gibi sınıf adını kullanmaktır.

Kategori `ILogger<T>` `T` olarak `ILogger` tam nitelikli tür adını kullanan bir örnek almak için kullanın:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Kategoriyi açıkça belirtmek için: `ILoggerFactory.CreateLogger`

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`tam nitelikli `CreateLogger` tür adı ile arama `T`eşdeğerdir.

## <a name="log-level"></a>Günlük düzeyi

Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir. Günlük düzeyi önem veya önemi gösterir. Örneğin, bir yöntem `Information` normal olarak sona erdiğinde bir `Warning` günlük ve bir yöntem *404 Bulunamadı* durum kodu döndürdüğünde bir günlük yazabilirsiniz.

Aşağıdaki kod oluşturur `Information` `Warning` ve günlükleri:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Önceki kodda, ilk parametre [Log olay kimliğidir.](#log-event-id) İkinci parametre, kalan yöntem parametreleri tarafından sağlanan bağımsız değişken değerleri için yer tutucuları içeren bir ileti şablonudur. Yöntem parametreleri bu makalenin ilerleyen bölümlerinde [ileti şablonu bölümünde](#log-message-template) açıklanmıştır.

Yöntem adındaki düzeyi içeren günlük yöntemleri (örneğin `LogWarning`ve) `LogInformation` [ILogger için uzantı yöntemleridir.](xref:Microsoft.Extensions.Logging.LoggerExtensions) Bu yöntemler, `Log` parametre `LogLevel` alan bir yöntem çağırır. `Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak sözdizimi nispeten karmaşıktır. Daha fazla bilgi <xref:Microsoft.Extensions.Logging.ILogger> için, bkz ve [logger uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core, burada en düşükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.

* İz = 0

  Genellikle yalnızca hata ayıklama için değerli olan bilgiler için. Bu iletiler hassas uygulama verileri içerebilir ve bu nedenle üretim ortamında etkinleştirilmemelidir. *Varsayılan olarak devre dışı bırakılır.*

* Hata Ayıklama = 1

  Geliştirme ve hata ayıklama yararlı olabilir bilgi için. Örnek: `Entering method Configure with flag set to true.` `Debug` Günlüklerin yüksek hacmi nedeniyle, yalnızca sorun giderme durumunda üretimde düzey günlüklerini etkinleştirin.

* Bilgi = 2

  Uygulamanın genel akışını izlemek için. Bu günlüklerin genellikle bazı uzun vadeli değeri vardır. Örnek: `Request received for path /api/todo`

* Uyarı = 3

  Uygulama akışındaki anormal veya beklenmeyen olaylar için. Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gereken hatalar veya diğer koşulları içerebilir. İşlenen özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir durumdur. Örnek: `FileNotFoundException for file quotes.txt.`

* Hata = 4

  İşlenemeyen hatalar ve özel durumlar için. Bu iletiler, uygulama genelinde bir hata değil, geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir. Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`

* Kritik = 5

  Acil müdahale gerektiren arızalar için. Örnekler: disk alanı dışında veri kaybı senaryoları.

Belirli bir depolama ortamına veya ekran penceresine ne kadar günlük çıkışı yazıldığını denetlemek için günlük düzeyini kullanın. Örneğin:

* Üretimde:
  * `Trace` Geçiş `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri üretir. Maliyetleri denetlemek ve veri depolama sınırlarını `Trace` `Information` aşmamak için, düzey iletileri ileyüksek hacimli, düşük maliyetli bir veri deposunda oturum açın.
  * Düzeyler `Warning` `Critical` arasında günlüğe kaydetme genellikle daha az, daha küçük günlük iletileri üretir. Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir, bu da veri deposu seçiminde daha fazla esneklik sağlar.
* Geliştirme sırasında:
  * İletileri konsola kaydedin. `Warning` `Critical`
  * Sorun `Trace` `Information` giderme iletileri ekleyin.

Bu makalenin daha sonra [günlük filtreleme](#log-filtering) bölümü, sağlayıcının işlediği günlük düzeylerini nasıl denetleyeceğini açıklar.

ASP.NET Core çerçeve olayları için günlükleri yazar. Bu makalede daha önceki günlük örnekleri `Information` düzeyin altındaki `Debug` `Trace` günlükleri hariç, bu nedenle hiçbir veya düzey günlükleri oluşturuldu. Aşağıda, günlükleri göstermek `Debug` üzere yapılandırılan örnek uygulama çalıştırılarak üretilen konsol günlüklerine bir örnek verilmiştir:

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a>Günlük olay kimliği

Her günlük bir *olay kimliği*belirtebilir. Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Olay kimliği, bir dizi olayı ilişkilendirer. Örneğin, bir sayfada öğelerin listesini görüntülemekle ilgili tüm günlükler 1001 olabilir.

Günlük sağlayıcısı olay kimliğini bir kimlik alanında, günlük iletisinde depolayabilir veya hiç depolamayabilir. Hata Ayıklama sağlayıcısı olay lı tazyikleri göstermez. Konsol sağlayıcısı, kategoriden sonra parantez içinde olay lı künyeleri gösterir:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Günlük ileti şablonu

Her günlük bir ileti şablonu belirtir. İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucular içerebilir. Yer tutucular için adları kullanın, sayılar için değil.

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Yer tutucuların sırası, adlarını değil, değerlerini sağlamak için hangi parametrelerin kullanıldığını belirler. Aşağıdaki kodda, parametre adlarının ileti şablonundaki sıranın dışında olduğuna dikkat edin:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Bu kod sırayla parametre değerleri ile bir günlük iletisi oluşturur:

```text
Parameter values: parm1, parm2
```

Günlük çerçevesi, günlük sağlayıcılarının yapılandırılmış [günlük olarak da bilinen anlamsal günlükleme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)yi uygulayabilmesi için bu şekilde çalışır. Bağımsız değişkenler, sadece biçimlendirilmiş ileti şablonuna değil, günlük sistemine aktarılır. Bu bilgiler, günlüğe kaydetme sağlayıcılarının parametre değerlerini alan olarak depolamasını sağlar. Örneğin, logger yöntemi çağrılarının şu şekilde olduğunu varsayalım:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Günlükleri Azure Tablo Depolama'ya gönderiyorsanız, her Azure `ID` Tablosu `RequestTime` kuruluşunun günlük verilerindeki sorguları basitleştiren özellikleri olabilir. Sorgu, metin iletisinin süresini `RequestTime` ayrıştmadan belirli bir aralıktaki tüm günlükleri bulabilir.

## <a name="logging-exceptions"></a>Günlüğe kaydetme özel durumları

Logger yöntemleri aşağıdaki örnekte olduğu gibi, bir özel durum geçmesine izin overloads var:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Farklı sağlayıcılar özel durum bilgilerini farklı şekillerde işler. Aşağıda, yukarıda gösterilen koddan Hata Ayıklama sağlayıcı çıktısı örneği verilmiştir.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Günlük filtreleme

Belirli bir sağlayıcı ve kategori veya tüm sağlayıcılar veya tüm kategoriler için minimum günlük düzeyi belirtebilirsiniz. Minimum düzeyin altındaki günlükler bu sağlayıcıya geçirilemedikleri için görüntülenmezler veya depolanırlar.

Tüm günlükleri bastırmak `LogLevel.None` için minimum günlük düzeyi olarak belirtin. İnsteger değeri `LogLevel.None` 6'dır ve `LogLevel.Critical` (5'ten) daha yüksektir.

### <a name="create-filter-rules-in-configuration"></a>Yapılandırmada filtre kuralları oluşturma

Proje şablonu `CreateDefaultBuilder` kodu, Konsol, Hata Ayıklama ve EventSource (ASP.NET Core 2.2 veya daha sonraki) sağlayıcıları için günlüğe kaydetmeyi ayarlamayı çağırır. Yöntem, `CreateDefaultBuilder` [bu makalede daha önce](#configuration) `Logging` açıklandığı gibi, bir bölümde yapılandırma aramak için günlük ayarlar.

Yapılandırma verileri, aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en az günlük düzeylerini belirtir:

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

Bu JSON altı filtre kuralı oluşturur: biri Hata Ayıklama sağlayıcısı için, dördü Konsol sağlayıcısı için ve diğeri de tüm sağlayıcılar için. Bir nesne oluşturulduğunda her sağlayıcı `ILogger` için tek bir kural seçilir.

### <a name="filter-rules-in-code"></a>Koddaki filtre kuralları

Aşağıdaki örnekte, filtre kurallarının kodda nasıl kaydedilen şekli gösterilmektedir:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

İkinci `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir. İlki, `AddFilter` sağlayıcı türünü belirtmediği için tüm sağlayıcılar için geçerlidir.

### <a name="how-filtering-rules-are-applied"></a>Filtreleme kuralları nasıl uygulanır?

Yapılandırma verileri ve `AddFilter` önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur. İlk altı yapılandırma örneğinden, son ikisi ise kod örneğinden gelir.

| Sayı | Sağlayıcı      | Ile başlayan kategoriler ...          | Minimum günlük düzeyi |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Hata ayıklama         | Tüm kategoriler                          | Bilgi       |
| 2      | Konsol       | Microsoft.AspNetCore.Mvc.Razor.Internal | Uyarı           |
| 3      | Konsol       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Hata ayıklama             |
| 4      | Konsol       | Microsoft.AspNetCore.Mvc.Razor          | Hata             |
| 5      | Konsol       | Tüm kategoriler                          | Bilgi       |
| 6      | Tüm sağlayıcılar | Tüm kategoriler                          | Hata ayıklama             |
| 7      | Tüm sağlayıcılar | Sistem                                  | Hata ayıklama             |
| 8      | Hata ayıklama         | Microsoft                               | İzleme             |

Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne sağlayıcı başına tek bir kural seçer. Bir `ILogger` örnek tarafından yazılan tüm iletiler seçili kurallara göre filtrelenir. Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.

Belirli bir kategori için bir `ILogger` bir oluşturulduğunda her sağlayıcı için aşağıdaki algoritma kullanılır:

* Sağlayıcıyla veya diğer adıyla eşleşen tüm kuralları seçin. Eşleşme bulunamazsa, boş bir sağlayıcıyla tüm kuralları seçin.
* Önceki adımın sonucundan, en uzun eşleşen kategori önekine sahip kuralları seçin. Eşleşme bulunamazsa, kategori belirtmeyen tüm kuralları seçin.
* Birden çok kural seçilirse, **sonuncusunu** alın.
* Kural seçili değilse, `MinimumLevel`kullanın.

Önceki kurallar listesiyle, "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" kategorisi için bir `ILogger` nesne oluşturduğunuzu varsayalım:

* Hata Ayıklama sağlayıcısı için 1, 6 ve 8 kuralları geçerlidir. Kural 8 çok özeldir, bu yüzden seçilen kuraldır.
* Konsol sağlayıcısı için 3, 4, 5 ve 6 kuralları geçerlidir. Kural 3 çok özeldir.

Ortaya çıkan `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzey ve üzeri günlükleri gönderir. `Debug` Seviye ve üzeri günlükler Konsol sağlayıcısına gönderilir.

### <a name="provider-aliases"></a>Sağlayıcı diğer adlar

Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *takma ad* tanımlar.  Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:

* Konsol
* Hata ayıklama
* EventSource
* Eventlog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Varsayılan minimum düzey

Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kurallar uygulanmadığı takdirde etkili olan minimum düzey ayarı vardır. Aşağıdaki örnek, minimum düzeyin nasıl ayarlanını gösterir:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Minimum düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, yani `Trace` ve `Debug` günlükleri yoksayılır.

### <a name="filter-functions"></a>Filtre işlevleri

Yapılandırma veya kod tarafından atanan kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır. İşlevdeki kod sağlayıcı türüne, kategorisine ve günlük düzeyine erişebilir. Örneğin:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a>Sistem kategorileri ve düzeyleri

ASP.NET Core ve Entity Framework Core tarafından kullanılan ve günlüklerin onlardan ne beklendiğine dair notlar içeren bazı kategoriler şunlardır:

| Kategori                            | Notlar |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | General ASP.NET Core teşhisi. |
| Microsoft.AspNetCore.DataProtection | Hangi anahtarlar düşünüldü, bulundu ve kullanıldı. |
| Microsoft.AspNetCore.HostFiltering  | Ev sahiplerine izin verildi. |
| Microsoft.AspNetCore.Hosting        | HTTP isteklerinin tamamlanması ne kadar sürer ve ne zaman başlar. Hangi barındırma başlangıç meclisleri yüklendi. |
| Microsoft.AspNetCore.Mvc            | MVC ve Razor teşhis. Model bağlama, filtre yürütme, görünüm derleme, eylem seçimi. |
| Microsoft.AspNetCore.Routing        | Rota eşleştirme bilgileri. |
| Microsoft.AspNetCore.Server         | Bağlantı başlatın, durdurun ve yanıtları canlı tutun. HTTPS sertifika bilgileri. |
| Microsoft.AspNetCore.StaticFiles    | Dosyalar servis edilebis. |
| Microsoft.EntityFrameworkCore       | Genel Varlık Çerçeve Çekirdek tanılama. Veritabanı etkinliği ve yapılandırma, değişiklik algılama, geçişler. |

## <a name="log-scopes"></a>Günlük kapsamları

 *Kapsam,* bir dizi mantıksal işlemi gruplayabilir. Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir. Örneğin, bir hareketi işlemenin bir parçası olarak oluşturulan her günlük, hareket kimliğini içerebilir.

Kapsam, `IDisposable` <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemle döndürülen ve bertaraf edilene kadar süren bir türdür. Logger aramalarını bir `using` blokta paketleyerek kapsam kullanın:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Aşağıdaki kod konsol sağlayıcısı için kapsamları sağlar:

*Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol kaydedici seçeneğini yapılandırmak gerekir.
>
> Yapılandırma hakkında daha fazla bilgi için [Yapılandırma](#configuration) bölümüne bakın.

Her günlük iletisi kapsamlı bilgileri içerir:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Yerleşik günlük sağlayıcıları

ASP.NET Core aşağıdaki sağlayıcıları gemiler:

* [Konsol](#console-provider)
* [Hata ayıklama](#debug-provider)
* [Olaykaynağı](#event-source-provider)
* [Eventlog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

ASP.NET Çekirdek Modülü ile stdout ve hata ayıklama <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>günlüğü hakkında bilgi için bkz. <xref:test/troubleshoot-azure-iis>

### <a name="console-provider"></a>Konsol sağlayıcısı

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi giriş çıkışını konsola gönderir. 

```csharp
logging.AddConsole();
```

Konsol günlüğe kaydetme çıktısını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Hata ayıklama sağlayıcısı

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) sınıfını (yöntem`Debug.WriteLine` çağrıları) kullanarak günlük çıktısını yazar.

Linux'ta, bu sağlayıcı */var/log/message'a*günlük ler yazar.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Olay Kaynak sağlayıcısı

[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi bir Event Source çapraz platform `Microsoft-Extensions-Logging`adı ile yazıyor. Windows'da sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.

```csharp
logging.AddEventSourceLogger();
```

Olay Kaynağı sağlayıcısı, ana `CreateDefaultBuilder` bilgisayarı oluşturmak için çağrıldığında otomatik olarak eklenir.

#### <a name="dotnet-trace-tooling"></a>dotnet izleme takım

[Dotnet izleme](/dotnet/core/diagnostics/dotnet-trace) aracı, çalışan bir işlemin .NET Core izlerinin toplanmasını sağlayan bir çapraz platform CLI global aracıdır. Araç <xref:Microsoft.Extensions.Logging.EventSource> sağlayıcı verilerini bir <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

Aşağıdaki komutla dotnet izleme aracını yükleyin:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Bir uygulamadan iz toplamak için dotnet izleme aracını kullanın:

1. Uygulama ana bilgisayarı `CreateDefaultBuilder`oluşturmuyorsa, Olay Kaynağı [sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.

1. Uygulamayı komutla `dotnet run` çalıştırın.

1. .NET Core uygulamasının işlem tanımlayıcısını (PID) belirleyin:

   * Windows'da aşağıdaki yaklaşımlardan birini kullanın:
     * Görev Yöneticisi (Ctrl+Alt+Del)
     * [görev listesi komutu](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process Powershell komutu](/powershell/module/microsoft.powershell.management/get-process)
   * Linux'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.

   Uygulamanın derlemesi ile aynı ada sahip işlem için PID'yi bulun.

1. Komutu `dotnet trace` uygulayın.

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

   PowerShell komut uyruğunu kullanırken, değeri tek tırnak içine alabilen `--providers` (`'`):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Windows olmayan platformlarda, `-f speedscope` çıktı izleme dosyasının biçimini `speedscope`''ye değiştirme seçeneğini ekleyin.

   | Anahtar kelime | Açıklama |
   | :-----: | ----------- |
   | 1       | Hakkında meta olayları `LoggingEventSource`günlüğe kaydedin. Olayları kaydetmez). `ILogger` |
   | 2       | Çağrıldığında `Message` `ILogger.Log()` olayı açar. Bilgileri programatik (biçimlendirilmemiş) bir şekilde sağlar. |
   | 4       | Çağrıldığında `FormatMessage` `ILogger.Log()` olayı açar. Bilgilerin biçimlendirilmiş dize sürümünü sağlar. |
   | 8       | Çağrıldığında `MessageJson` `ILogger.Log()` olayı açar. Bağımsız değişkenlerin JSON temsilini sağlar. |

   | Etkinlik Düzeyi | Açıklama     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`için `{Logger Category}` girişler ve `{Event Level}` ek günlük filtreleme koşulları temsil. Bir `FilterSpecs` semicolon ile`;`ayrı girişleri ( ).

   Windows komut uyruğunu kullanarak örnek `--providers` (değer etrafında tek bir tırnak işareti**yok):**

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Önceki komut etkinleştirir:

   * Hatalar için biçimlendirilmiş dizeleri (`4`) üretmek`2`için Olay Kaynağı kaydedici ( ).
   * `Microsoft.AspNetCore.Hosting``Informational` günlük düzeyinde günlüğe`4`kaydetme ( ).

1. Enter tuşuna veya Ctrl+C tuşuna basarak dotnet izleme aletini durdurun.

   İzleme, `dotnet trace` komutun yürütüldüğü klasöre *trace.nettrace* adı ile kaydedilir.

1. [Perfview](#perfview)ile iz açın. *trace.nettrace* dosyasını açın ve izleme olaylarını keşfedin.

Daha fazla bilgi için bkz.

* [Performans analizi yardımcı programı (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core dokümantasyon) için izleme
* [Performans analizi yardımcı programı (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub depo belgeleri) için izleme
* [LoggingEventSource Sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Tarayıcısı)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource referans kaynağı (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Farklı bir sürüm için referans `release/{Version}`kaynağı `{Version}` elde etmek için, şubeyi , ASP.NET Core sürümü istenilen nerede değiştirin.
* [Perfview](#perfview) &ndash; Olay Kaynağı izlerini görüntülemek için kullanışlıdır.

#### <a name="perfview"></a>Perfview

Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın. ETW günlüklerini görüntülemek için başka araçlar da vardır, ancak PerfView, ASP.NET Core tarafından yayılan ETW etkinlikleri ile çalışmak için en iyi deneyimi sağlar.

PerfView'i bu sağlayıcı tarafından günlüğe kaydedilen olayları `*Microsoft-Extensions-Logging` toplamak için yapılandırmak için dizeyi **Ek Sağlayıcılar** listesine ekleyin. (Dize başında yıldız işaretini kaçırmayın.)

![Perfview Ek Sağlayıcılar](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog sağlayıcısı

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi günlük çıktısını Windows Olay Günlüğü'ne gönderir.

```csharp
logging.AddEventLog();
```

[AddEventLog aşırı yükleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>geçmenizi sağlar. `null` Belirtilmişse veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:

* `LogName`&ndash; "Uygulama"
* `SourceName`&ndash; ".NET Çalışma Zamanı"
* `MachineName`&ndash; yerel makine

Olaylar [Uyarı düzeyi ve daha yüksek](#log-level)için günlüğe kaydedilir. Olayları daha `Warning`düşük günlüğe kaydetmek için, günlük düzeyini açıkça ayarlayın. Örneğin, *appsettings.json* dosyasına aşağıdakileri ekleyin:

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>TraceSource sağlayıcısı

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıkları ve sağlayıcıları kullanır.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) bir kaynak anahtarı ve bir izleme dinleyici geçmesine izin.

Bu sağlayıcıyı kullanmak için bir uygulamanın .NET Framework(.NET Core yerine) üzerinde çalışması gerekiyor. Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan lar gibi çeşitli [dinleyicilere](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.

### <a name="azure-app-service-provider"></a>Azure Uygulama Hizmeti sağlayıcısı

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına günlükler yazar ve bir Azure Depolama hesabında [depolamayı şişirmek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) için.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Sağlayıcı paketi paylaşılan çerçeveye dahil değildir. Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.

Sağlayıcı ayarlarını yapılandırmak <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>için aşağıdaki örnekte gösterildiği gibi kullanın ve kullanın:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

Bir Uygulama Hizmeti uygulamasına deploy yaptığınızda, uygulama, Azure portalının **Uygulama Hizmeti** sayfasının Uygulama [Hizmeti günlükleri](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarlara saygı gösterir. Aşağıdaki ayarlar güncelleştirildiğinde, değişiklikler uygulamanın yeniden başlatılmasına veya yeniden dağıtılmasına gerek kalmadan hemen etkinolur.

* **Uygulama Günlüğü (Filesystem)**
* **Uygulama Günlüğü (Blob)**

Günlük dosyaları için varsayılan konum *\\D:\\ana\\LogFiles Application* klasöründe ve varsayılan dosya adı *tanılama-yyyymmdd.txt*. Varsayılan dosya boyutu sınırı 10 MB'dır ve tutulan varsayılan maksimum dosya sayısı 2'dir. Varsayılan blob adı *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*olduğunu.

Sağlayıcı yalnızca proje Azure ortamında çalıştığında çalışır. Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya yerel geliştirme depolamasına blobs için yazmaz hiçbir etkisi yoktur.

#### <a name="azure-log-streaming"></a>Azure günlük akışı

Azure günlük akışı, günlük etkinliğini gerçek zamanlı olarak görüntülemenizi sağlar:

* Uygulama sunucusu
* Web sunucusu
* Başarısız istek izleme

Azure günlük akışını yapılandırmak için:

* Uygulamanızın portal sayfasından **Uygulama Hizmeti günlükleri** sayfasına gidin.
* **Uygulama Günlüğe Kaydetme (Filesystem)** 'yi A.B.K.'ya ayarlayın. **On**
* Günlük **Düzeyi'ni**seçin. Bu ayar, uygulamadaki diğer günlük sağlayıcıları için değil, yalnızca Azure günlük akışı için geçerlidir.

Uygulama mesajlarını görüntülemek için **Günlük Akışı** sayfasına gidin. Arayüz üzerinden `ILogger` uygulama tarafından kaydedilirler.

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights izleme günlüğü

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi Azure Application Insights'a günlük ler yazar. Application Insights, bir web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir. Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve çözümleyebilirsiniz.

Günlük sağlayıcısı [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)bir bağımlılık olarak ASP.NET Core için tüm kullanılabilir telemetri sağlayan bir paket olarak dahildir. Bu paketi kullanıyorsanız, sağlayıcı paketini yüklemeniz gerekmez.

ASP.NET [4.x için microsoft.applicationinsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Application Insights'a genel bakış](/azure/application-insights/app-insights-overview)
* [ASP.NET Core uygulamaları için Uygulama Öngörüleri](/azure/azure-monitor/app/asp-net-core) - Günlük le birlikte tüm Uygulama Öngörüleri telemetrisini uygulamak istiyorsanız buradan başlayın.
* [.NET Core ILogger günlükleri için ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - Application Insights telemetri geri kalanı olmadan günlük sağlayıcı uygulamak istiyorsanız buraya başlayın.
* [Uygulama Insights günlük bağdaştırıcıları](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* Microsoft Learn sitesindeki Uygulama Öngörüleri SDK - Etkileşimli öğreticiyi [yükleyin, yapılandırın ve başlayın.](/learn/modules/instrument-web-app-code-with-application-insights)

## <a name="third-party-logging-providers"></a>Üçüncü taraf günlük sağlayıcıları

ASP.NET Core ile çalışan üçüncü taraf günlük çerçeveleri:

* [elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))
* [Nöbetçi](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))

Bazı üçüncü taraf [çerçeveleri, yapılandırılmış günlük olarak da bilinen anlamsal günlük](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)gerçekleştirebilirsiniz.

Üçüncü taraf çerçevesi kullanmak, yerleşik sağlayıcılardan birini kullanmaya benzer:

1. Projenize bir NuGet paketi ekleyin.
1. Günlük `ILoggerFactory` çerçevesi tarafından sağlanan bir uzantı yöntemi çağırın.

Daha fazla bilgi için her sağlayıcının belgelerine bakın. Üçüncü taraf günlük sağlayıcıları Microsoft tarafından desteklenmez.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/)

.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler. Bu makalede, yerleşik sağlayıcılarla günlük API'sinin nasıl kullanılacağı gösterilmektedir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Sağlayıcılar ekleme

Bir günlük sağlayıcısı günlükleri görüntüler veya depolar. Örneğin, Konsol sağlayıcısı konsolda günlükleri görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Uygulama Öngörüleri'nde depolar. Günlükler, birden çok sağlayıcı eklenerek birden çok hedefe gönderilebilir.

Sağlayıcı eklemek için `Add{provider name}` *sağlayıcının*uzatma yöntemini Program.cs olarak arayın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Önceki kod için başvurular `Microsoft.Extensions.Logging` `Microsoft.Extensions.Configuration`ve .

Varsayılan proje şablonu çağrıları <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, aşağıdaki günlük sağlayıcıları ekler:

* Konsol
* Hata ayıklama
* EventSource (Core 2.2ASP.NETden itibaren)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Kullanırsanız, `CreateDefaultBuilder`varsayılan sağlayıcıları kendi seçeneklerinizle değiştirebilirsiniz. Arama <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>ve istediğiniz sağlayıcıları ekleyin.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Makalenin ilerleyen saatlerinde [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve üçüncü taraf günlük [sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.

## <a name="create-logs"></a>Günlükler oluşturma

Günlükleri oluşturmak için <xref:Microsoft.Extensions.Logging.ILogger%601> bir nesne kullanın. Bir web uygulamasında veya barındırılan bir hizmette, bağımlılık enjeksiyonundan (DI) bir `ILogger` uygulama alın. Ana bilgisayar olmayan konsol uygulamalarında, `LoggerFactory` `ILogger`bir .

Aşağıdaki ASP.NET Core örneği, kategori `TodoApiSample.Pages.AboutModel` olarak bir logger oluşturur. Günlük *kategorisi,* her günlükle ilişkili bir dizedir. DI `ILogger<T>` tarafından sağlanan örnek, kategori olarak tam nitelikli `T` türü ada sahip günlükleri oluşturur. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, logger düzeyi `Information` olarak günlükleri oluşturmak için kullanılır. Günlük *düzeyi,* günlüğe kaydedilen olayın önem derecesini gösterir.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Düzeyleri](#log-level) ve [kategorileri](#log-category) daha ayrıntılı olarak bu makalede açıklanmıştır.

### <a name="create-logs-in-startup"></a>Başlangıç'ta günlük oluşturma

`Startup` Sınıfa günlük yazmak için, `ILogger` oluşturucu imzasına bir parametre ekleyin:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Program sınıfında günlük oluşturma

`Program` Sınıfa günlük yazmak için DI'den bir `ILogger` örnek alın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Ana bilgisayar inşaatı sırasında oturum açma doğrudan desteklenmez. Ancak, ayrı bir logger kullanılabilir. Aşağıdaki örnekte, bir [Serilog](https://serilog.net/) kaydedici oturum `CreateWebHostBuilder`açmak için kullanılır. `AddSerilog`belirtilen statik konfigürasyonu `Log.Logger`kullanır:

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

### <a name="no-asynchronous-logger-methods"></a>Asynchronous logger yöntemleri yok

Günlüğe kaydetme, eşzamanlı kodun performans maliyetine değmeyecek kadar hızlı olmalıdır. Günlük veri deponuz yavaşsa, doğrudan yazmayın. Günlük iletilerini başlangıçta hızlı bir mağazaya yazmayı düşünün, ardından yavaş mağazaya taşıyın. Örneğin, SQL Server'a günlüğe kaydoluyorsanız, yöntemler eşzamanlı olduğundan `Log` bunu doğrudan `Log` bir yöntemle yapmak istemezsiniz. Bunun yerine, eşzamanlı olarak bellek içi sıraya günlük iletileri ekleyin ve bir arka plan çalışanı sql server veri itme asynchronous iş yapmak için sıranın dışına iletileri çekin. Daha fazla bilgi için [bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.

## <a name="configuration"></a>Yapılandırma

Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:

* Dosya biçimleri (INI, JSON ve XML).
* Komut satırı bağımsız değişkenleri.
* Ortam değişkenleri.
* Bellek içi .NET nesneleri.
* Şifrelenmemiş [Gizli Yönetici](xref:security/app-secrets) depolama.
* [Azure Anahtar Kasası](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.
* Özel sağlayıcılar (yüklü veya oluşturulmuş).

Örneğin, günlüğe kaydetme yapılandırması `Logging` genellikle uygulama ayarları dosyaları bölümü tarafından sağlanır. Aşağıdaki örnekte, tipik bir uygulamanın içeriği *gösterilmektedir. Development.json* dosyası:

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

Özellik `Logging` olabilir `LogLevel` ve günlük sağlayıcı özellikleri (Konsol gösterilir).

Aşağıdaki `LogLevel` `Logging` özellik, seçili kategoriler için günlüğe kaydolacak minimum [düzey](#log-level) belirtir. Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe, diğer `Debug` tüm düzeylerde günlüğe kaydedin.

Altında `Logging` diğer özellikler günlük sağlayıcıları belirtir. Örnek Konsol sağlayıcısı içindir. Bir sağlayıcı [günlük kapsamlarını destekliyorsa,](#log-scopes) `IncludeScopes` etkin olup olmadıklarını gösterir. Bir sağlayıcı özelliği `Console` (örneğin gibi) da `LogLevel` bir özellik belirtebilir. `LogLevel`bir sağlayıcı altında bu sağlayıcı için oturum açmak için düzeyleri belirtir.

Düzeyler belirtilirse, `Logging.{providername}.LogLevel`'de `Logging.LogLevel`ayarlanan bir şeyi geçersiz kılarlar.

Günlük API'si, bir uygulama çalışırken günlük düzeylerini değiştirmek için bir senaryo içermez. Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu da günlüğe kaydetme yapılandırması üzerinde hemen etkili olur. Örneğin, ayarlar dosyalarını okumak `CreateDefaultBuilder` için eklenen Dosya Yapılandırma [Sağlayıcısı,](xref:fundamentals/configuration/index#file-configuration-provider)varsayılan olarak günlük yapılandırmasını yeniden yükler. Bir uygulama çalışırken yapılandırma kod olarak değiştirilirse, uygulama uygulamanın günlük yapılandırmasını güncellemek için [IConfigurationRoot.Reload'ı](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) arayabilir.

Yapılandırma sağlayıcılarının uygulanması hakkında <xref:fundamentals/configuration/index>bilgi için bkz.

## <a name="sample-logging-output"></a>Örnek günlük çıktısı

Önceki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında günlükler konsolda görünür. Konsol çıkışına bir örnek aşağıda verilmiştir:

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

Önceki günlükler, `http://localhost:5000/api/todo/0`örnek uygulamaya http get isteği yaparak oluşturuldu.

Örnek uygulamayı Visual Studio'da çalıştırdığınızda Hata Ayıklama penceresinde görünen günlüklerin bir örneği aşağıda verilmiştir:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Önceki bölümde gösterilen `ILogger` aramalar tarafından oluşturulan günlükleri "TodoApi" ile başlar. "Microsoft" kategorileriyle başlayan günlükler ASP.NET Çekirdek çerçeve kodundan gelir. ASP.NET Core ve uygulama kodu aynı günlük API ve sağlayıcıları kullanıyor.

Bu makalenin geri kalanı, günlüğe kaydetme için bazı ayrıntıları ve seçenekleri açıklar.

## <a name="nuget-packages"></a>NuGet paketleri

Ve arabirimler [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)ve onlar için varsayılan uygulamalar [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)bulunmaktadır. `ILoggerFactory` `ILogger`

## <a name="log-category"></a>Günlük kategorisi

Bir `ILogger` nesne oluşturulduğunda, bunun için bir *kategori* belirtilir. Bu kategori, bu örnek tarafından oluşturulan her `ILogger`günlük iletisi ile birlikte verilir. Kategori herhangi bir dize olabilir, ancak kural "TodoApi.Controllers.TodoController" gibi sınıf adını kullanmaktır.

Kategori `ILogger<T>` `T` olarak `ILogger` tam nitelikli tür adını kullanan bir örnek almak için kullanın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Kategoriyi açıkça belirtmek için: `ILoggerFactory.CreateLogger`

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`tam nitelikli `CreateLogger` tür adı ile arama `T`eşdeğerdir.

## <a name="log-level"></a>Günlük düzeyi

Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir. Günlük düzeyi önem veya önemi gösterir. Örneğin, bir yöntem `Information` normal olarak sona erdiğinde bir `Warning` günlük ve bir yöntem *404 Bulunamadı* durum kodu döndürdüğünde bir günlük yazabilirsiniz.

Aşağıdaki kod oluşturur `Information` `Warning` ve günlükleri:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Önceki kodda, ilk parametre [Log olay kimliğidir.](#log-event-id) İkinci parametre, kalan yöntem parametreleri tarafından sağlanan bağımsız değişken değerleri için yer tutucuları içeren bir ileti şablonudur. Yöntem parametreleri bu makalenin ilerleyen bölümlerinde [ileti şablonu bölümünde](#log-message-template) açıklanmıştır.

Yöntem adındaki düzeyi içeren günlük yöntemleri (örneğin `LogWarning`ve) `LogInformation` [ILogger için uzantı yöntemleridir.](xref:Microsoft.Extensions.Logging.LoggerExtensions) Bu yöntemler, `Log` parametre `LogLevel` alan bir yöntem çağırır. `Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak sözdizimi nispeten karmaşıktır. Daha fazla bilgi <xref:Microsoft.Extensions.Logging.ILogger> için, bkz ve [logger uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core, burada en düşükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.

* İz = 0

  Genellikle yalnızca hata ayıklama için değerli olan bilgiler için. Bu iletiler hassas uygulama verileri içerebilir ve bu nedenle üretim ortamında etkinleştirilmemelidir. *Varsayılan olarak devre dışı bırakılır.*

* Hata Ayıklama = 1

  Geliştirme ve hata ayıklama yararlı olabilir bilgi için. Örnek: `Entering method Configure with flag set to true.` `Debug` Günlüklerin yüksek hacmi nedeniyle, yalnızca sorun giderme durumunda üretimde düzey günlüklerini etkinleştirin.

* Bilgi = 2

  Uygulamanın genel akışını izlemek için. Bu günlüklerin genellikle bazı uzun vadeli değeri vardır. Örnek: `Request received for path /api/todo`

* Uyarı = 3

  Uygulama akışındaki anormal veya beklenmeyen olaylar için. Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gereken hatalar veya diğer koşulları içerebilir. İşlenen özel durumlar `Warning` günlük düzeyini kullanmak için yaygın bir durumdur. Örnek: `FileNotFoundException for file quotes.txt.`

* Hata = 4

  İşlenemeyen hatalar ve özel durumlar için. Bu iletiler, uygulama genelinde bir hata değil, geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir. Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`

* Kritik = 5

  Acil müdahale gerektiren arızalar için. Örnekler: disk alanı dışında veri kaybı senaryoları.

Belirli bir depolama ortamına veya ekran penceresine ne kadar günlük çıkışı yazıldığını denetlemek için günlük düzeyini kullanın. Örneğin:

* Üretimde:
  * `Trace` Geçiş `Information` düzeylerinde günlüğe kaydetme, yüksek hacimli ayrıntılı günlük iletileri üretir. Maliyetleri denetlemek ve veri depolama sınırlarını `Trace` `Information` aşmamak için, düzey iletileri ileyüksek hacimli, düşük maliyetli bir veri deposunda oturum açın.
  * Düzeyler `Warning` `Critical` arasında günlüğe kaydetme genellikle daha az, daha küçük günlük iletileri üretir. Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir, bu da veri deposu seçiminde daha fazla esneklik sağlar.
* Geliştirme sırasında:
  * İletileri konsola kaydedin. `Warning` `Critical`
  * Sorun `Trace` `Information` giderme iletileri ekleyin.

Bu makalenin daha sonra [günlük filtreleme](#log-filtering) bölümü, sağlayıcının işlediği günlük düzeylerini nasıl denetleyeceğini açıklar.

ASP.NET Core çerçeve olayları için günlükleri yazar. Bu makalede daha önceki günlük örnekleri `Information` düzeyin altındaki `Debug` `Trace` günlükleri hariç, bu nedenle hiçbir veya düzey günlükleri oluşturuldu. Aşağıda, günlükleri göstermek `Debug` üzere yapılandırılan örnek uygulama çalıştırılarak üretilen konsol günlüklerine bir örnek verilmiştir:

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

## <a name="log-event-id"></a>Günlük olay kimliği

Her günlük bir *olay kimliği*belirtebilir. Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Olay kimliği, bir dizi olayı ilişkilendirer. Örneğin, bir sayfada öğelerin listesini görüntülemekle ilgili tüm günlükler 1001 olabilir.

Günlük sağlayıcısı olay kimliğini bir kimlik alanında, günlük iletisinde depolayabilir veya hiç depolamayabilir. Hata Ayıklama sağlayıcısı olay lı tazyikleri göstermez. Konsol sağlayıcısı, kategoriden sonra parantez içinde olay lı künyeleri gösterir:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Günlük ileti şablonu

Her günlük bir ileti şablonu belirtir. İleti şablonu, bağımsız değişkenlerin sağlandığı yer tutucular içerebilir. Yer tutucular için adları kullanın, sayılar için değil.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Yer tutucuların sırası, adlarını değil, değerlerini sağlamak için hangi parametrelerin kullanıldığını belirler. Aşağıdaki kodda, parametre adlarının ileti şablonundaki sıranın dışında olduğuna dikkat edin:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Bu kod sırayla parametre değerleri ile bir günlük iletisi oluşturur:

```text
Parameter values: parm1, parm2
```

Günlük çerçevesi, günlük sağlayıcılarının yapılandırılmış [günlük olarak da bilinen anlamsal günlükleme](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)yi uygulayabilmesi için bu şekilde çalışır. Bağımsız değişkenler, sadece biçimlendirilmiş ileti şablonuna değil, günlük sistemine aktarılır. Bu bilgiler, günlüğe kaydetme sağlayıcılarının parametre değerlerini alan olarak depolamasını sağlar. Örneğin, logger yöntemi çağrılarının şu şekilde olduğunu varsayalım:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Günlükleri Azure Tablo Depolama'ya gönderiyorsanız, her Azure `ID` Tablosu `RequestTime` kuruluşunun günlük verilerindeki sorguları basitleştiren özellikleri olabilir. Sorgu, metin iletisinin süresini `RequestTime` ayrıştmadan belirli bir aralıktaki tüm günlükleri bulabilir.

## <a name="logging-exceptions"></a>Günlüğe kaydetme özel durumları

Logger yöntemleri aşağıdaki örnekte olduğu gibi, bir özel durum geçmesine izin overloads var:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Farklı sağlayıcılar özel durum bilgilerini farklı şekillerde işler. Aşağıda, yukarıda gösterilen koddan Hata Ayıklama sağlayıcı çıktısı örneği verilmiştir.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Günlük filtreleme

Belirli bir sağlayıcı ve kategori veya tüm sağlayıcılar veya tüm kategoriler için minimum günlük düzeyi belirtebilirsiniz. Minimum düzeyin altındaki günlükler bu sağlayıcıya geçirilemedikleri için görüntülenmezler veya depolanırlar.

Tüm günlükleri bastırmak `LogLevel.None` için minimum günlük düzeyi olarak belirtin. İnsteger değeri `LogLevel.None` 6'dır ve `LogLevel.Critical` (5'ten) daha yüksektir.

### <a name="create-filter-rules-in-configuration"></a>Yapılandırmada filtre kuralları oluşturma

Proje şablonu `CreateDefaultBuilder` kodu, Konsol, Hata Ayıklama ve EventSource (ASP.NET Core 2.2 veya daha sonraki) sağlayıcıları için günlüğe kaydetmeyi ayarlamayı çağırır. Yöntem, `CreateDefaultBuilder` [bu makalede daha önce](#configuration) `Logging` açıklandığı gibi, bir bölümde yapılandırma aramak için günlük ayarlar.

Yapılandırma verileri, aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en az günlük düzeylerini belirtir:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Bu JSON altı filtre kuralı oluşturur: biri Hata Ayıklama sağlayıcısı için, dördü Konsol sağlayıcısı için ve diğeri de tüm sağlayıcılar için. Bir nesne oluşturulduğunda her sağlayıcı `ILogger` için tek bir kural seçilir.

### <a name="filter-rules-in-code"></a>Koddaki filtre kuralları

Aşağıdaki örnekte, filtre kurallarının kodda nasıl kaydedilen şekli gösterilmektedir:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

İkinci `AddFilter` hata ayıklama sağlayıcısını tür adını kullanarak belirtir. İlki, `AddFilter` sağlayıcı türünü belirtmediği için tüm sağlayıcılar için geçerlidir.

### <a name="how-filtering-rules-are-applied"></a>Filtreleme kuralları nasıl uygulanır?

Yapılandırma verileri ve `AddFilter` önceki örneklerde gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur. İlk altı yapılandırma örneğinden, son ikisi ise kod örneğinden gelir.

| Sayı | Sağlayıcı      | Ile başlayan kategoriler ...          | Minimum günlük düzeyi |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Hata ayıklama         | Tüm kategoriler                          | Bilgi       |
| 2      | Konsol       | Microsoft.AspNetCore.Mvc.Razor.Internal | Uyarı           |
| 3      | Konsol       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Hata ayıklama             |
| 4      | Konsol       | Microsoft.AspNetCore.Mvc.Razor          | Hata             |
| 5      | Konsol       | Tüm kategoriler                          | Bilgi       |
| 6      | Tüm sağlayıcılar | Tüm kategoriler                          | Hata ayıklama             |
| 7      | Tüm sağlayıcılar | Sistem                                  | Hata ayıklama             |
| 8      | Hata ayıklama         | Microsoft                               | İzleme             |

Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne sağlayıcı başına tek bir kural seçer. Bir `ILogger` örnek tarafından yazılan tüm iletiler seçili kurallara göre filtrelenir. Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.

Belirli bir kategori için bir `ILogger` bir oluşturulduğunda her sağlayıcı için aşağıdaki algoritma kullanılır:

* Sağlayıcıyla veya diğer adıyla eşleşen tüm kuralları seçin. Eşleşme bulunamazsa, boş bir sağlayıcıyla tüm kuralları seçin.
* Önceki adımın sonucundan, en uzun eşleşen kategori önekine sahip kuralları seçin. Eşleşme bulunamazsa, kategori belirtmeyen tüm kuralları seçin.
* Birden çok kural seçilirse, **sonuncusunu** alın.
* Kural seçili değilse, `MinimumLevel`kullanın.

Önceki kurallar listesiyle, "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" kategorisi için bir `ILogger` nesne oluşturduğunuzu varsayalım:

* Hata Ayıklama sağlayıcısı için 1, 6 ve 8 kuralları geçerlidir. Kural 8 çok özeldir, bu yüzden seçilen kuraldır.
* Konsol sağlayıcısı için 3, 4, 5 ve 6 kuralları geçerlidir. Kural 3 çok özeldir.

Ortaya çıkan `ILogger` örnek, `Trace` hata ayıklama sağlayıcısına düzey ve üzeri günlükleri gönderir. `Debug` Seviye ve üzeri günlükler Konsol sağlayıcısına gönderilir.

### <a name="provider-aliases"></a>Sağlayıcı diğer adlar

Her sağlayıcı, tam nitelikli tür adı yerine yapılandırmada kullanılabilecek bir *takma ad* tanımlar.  Yerleşik sağlayıcılar için aşağıdaki diğer adları kullanın:

* Konsol
* Hata ayıklama
* EventSource
* Eventlog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Varsayılan minimum düzey

Yalnızca belirli bir sağlayıcı ve kategori için yapılandırma veya koddan kurallar uygulanmadığı takdirde etkili olan minimum düzey ayarı vardır. Aşağıdaki örnek, minimum düzeyin nasıl ayarlanını gösterir:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Minimum düzeyi açıkça ayarlamazsanız, varsayılan değer `Information`, yani `Trace` ve `Debug` günlükleri yoksayılır.

### <a name="filter-functions"></a>Filtre işlevleri

Yapılandırma veya kod tarafından atanan kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağrılır. İşlevdeki kod sağlayıcı türüne, kategorisine ve günlük düzeyine erişebilir. Örneğin:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Sistem kategorileri ve düzeyleri

ASP.NET Core ve Entity Framework Core tarafından kullanılan ve günlüklerin onlardan ne beklendiğine dair notlar içeren bazı kategoriler şunlardır:

| Kategori                            | Notlar |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | General ASP.NET Core teşhisi. |
| Microsoft.AspNetCore.DataProtection | Hangi anahtarlar düşünüldü, bulundu ve kullanıldı. |
| Microsoft.AspNetCore.HostFiltering  | Ev sahiplerine izin verildi. |
| Microsoft.AspNetCore.Hosting        | HTTP isteklerinin tamamlanması ne kadar sürer ve ne zaman başlar. Hangi barındırma başlangıç meclisleri yüklendi. |
| Microsoft.AspNetCore.Mvc            | MVC ve Razor teşhis. Model bağlama, filtre yürütme, görünüm derleme, eylem seçimi. |
| Microsoft.AspNetCore.Routing        | Rota eşleştirme bilgileri. |
| Microsoft.AspNetCore.Server         | Bağlantı başlatın, durdurun ve yanıtları canlı tutun. HTTPS sertifika bilgileri. |
| Microsoft.AspNetCore.StaticFiles    | Dosyalar servis edilebis. |
| Microsoft.EntityFrameworkCore       | Genel Varlık Çerçeve Çekirdek tanılama. Veritabanı etkinliği ve yapılandırma, değişiklik algılama, geçişler. |

## <a name="log-scopes"></a>Günlük kapsamları

 *Kapsam,* bir dizi mantıksal işlemi gruplayabilir. Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir. Örneğin, bir hareketi işlemenin bir parçası olarak oluşturulan her günlük, hareket kimliğini içerebilir.

Kapsam, `IDisposable` <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemle döndürülen ve bertaraf edilene kadar süren bir türdür. Logger aramalarını bir `using` blokta paketleyerek kapsam kullanın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Aşağıdaki kod konsol sağlayıcısı için kapsamları sağlar:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol kaydedici seçeneğini yapılandırmak gerekir.
>
> Yapılandırma hakkında daha fazla bilgi için [Yapılandırma](#configuration) bölümüne bakın.

Her günlük iletisi kapsamlı bilgileri içerir:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Yerleşik günlük sağlayıcıları

ASP.NET Core aşağıdaki sağlayıcıları gemiler:

* [Konsol](#console-provider)
* [Hata ayıklama](#debug-provider)
* [Olaykaynağı](#event-source-provider)
* [Eventlog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

ASP.NET Çekirdek Modülü ile stdout ve hata ayıklama <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>günlüğü hakkında bilgi için bkz. <xref:test/troubleshoot-azure-iis>

### <a name="console-provider"></a>Konsol sağlayıcısı

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sağlayıcı paketi giriş çıkışını konsola gönderir. 

```csharp
logging.AddConsole();
```

Konsol günlüğe kaydetme çıktısını görmek için proje klasöründe bir komut istemi açın ve aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Hata ayıklama sağlayıcısı

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) sınıfını (yöntem`Debug.WriteLine` çağrıları) kullanarak günlük çıktısını yazar.

Linux'ta, bu sağlayıcı */var/log/message'a*günlük ler yazar.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Olay Kaynak sağlayıcısı

[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi bir Event Source çapraz platform `Microsoft-Extensions-Logging`adı ile yazıyor. Windows'da sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.

```csharp
logging.AddEventSourceLogger();
```

Olay Kaynağı sağlayıcısı, ana `CreateDefaultBuilder` bilgisayarı oluşturmak için çağrıldığında otomatik olarak eklenir.

Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın. ETW günlüklerini görüntülemek için başka araçlar da vardır, ancak PerfView, ASP.NET Core tarafından yayılan ETW etkinlikleri ile çalışmak için en iyi deneyimi sağlar.

PerfView'i bu sağlayıcı tarafından günlüğe kaydedilen olayları `*Microsoft-Extensions-Logging` toplamak için yapılandırmak için dizeyi **Ek Sağlayıcılar** listesine ekleyin. (Dize başında yıldız işaretini kaçırmayın.)

![Perfview Ek Sağlayıcılar](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog sağlayıcısı

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi günlük çıktısını Windows Olay Günlüğü'ne gönderir.

```csharp
logging.AddEventLog();
```

[AddEventLog aşırı yükleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>geçmenizi sağlar. `null` Belirtilmişse veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:

* `LogName`&ndash; "Uygulama"
* `SourceName`&ndash; ".NET Çalışma Zamanı"
* `MachineName`&ndash; yerel makine

Olaylar [Uyarı düzeyi ve daha yüksek](#log-level)için günlüğe kaydedilir. Olayları daha `Warning`düşük günlüğe kaydetmek için, günlük düzeyini açıkça ayarlayın. Örneğin, *appsettings.json* dosyasına aşağıdakileri ekleyin:

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>TraceSource sağlayıcısı

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıkları ve sağlayıcıları kullanır.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) bir kaynak anahtarı ve bir izleme dinleyici geçmesine izin.

Bu sağlayıcıyı kullanmak için bir uygulamanın .NET Framework(.NET Core yerine) üzerinde çalışması gerekiyor. Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan lar gibi çeşitli [dinleyicilere](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.

### <a name="azure-app-service-provider"></a>Azure Uygulama Hizmeti sağlayıcısı

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına günlükler yazar ve bir Azure Depolama hesabında [depolamayı şişirmek](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) için.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Sağlayıcı paketi [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahil değildir. .NET Framework'u hedef alırken `Microsoft.AspNetCore.App` veya meta pakete başvururken, sağlayıcı paketini projeye ekleyin. 

Aşırı <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> yükleme içeri geçmeni <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>sağlar. Ayarlar nesnesi, günlüğe kaydetme çıktısı şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir. (*Çıktı şablonu,* bir yöntem çağrısıyla sağlanana ek olarak tüm `ILogger` günlüklere uygulanan bir ileti şablonudur.)

Bir Uygulama Hizmeti uygulamasına deploy yaptığınızda, uygulama, Azure portalının **Uygulama Hizmeti** sayfasının Uygulama [Hizmeti günlükleri](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarlara saygı gösterir. Aşağıdaki ayarlar güncelleştirildiğinde, değişiklikler uygulamanın yeniden başlatılmasına veya yeniden dağıtılmasına gerek kalmadan hemen etkinolur.

* **Uygulama Günlüğü (Filesystem)**
* **Uygulama Günlüğü (Blob)**

Günlük dosyaları için varsayılan konum *\\D:\\ana\\LogFiles Application* klasöründe ve varsayılan dosya adı *tanılama-yyyymmdd.txt*. Varsayılan dosya boyutu sınırı 10 MB'dır ve tutulan varsayılan maksimum dosya sayısı 2'dir. Varsayılan blob adı *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*olduğunu.

Sağlayıcı yalnızca proje Azure ortamında çalıştığında çalışır. Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya yerel geliştirme depolamasına blobs için yazmaz hiçbir etkisi yoktur.

#### <a name="azure-log-streaming"></a>Azure günlük akışı

Azure günlük akışı, günlük etkinliğini gerçek zamanlı olarak görüntülemenizi sağlar:

* Uygulama sunucusu
* Web sunucusu
* Başarısız istek izleme

Azure günlük akışını yapılandırmak için:

* Uygulamanızın portal sayfasından **Uygulama Hizmeti günlükleri** sayfasına gidin.
* **Uygulama Günlüğe Kaydetme (Filesystem)** 'yi A.B.K.'ya ayarlayın. **On**
* Günlük **Düzeyi'ni**seçin. Bu ayar, uygulamadaki diğer günlük sağlayıcıları için değil, yalnızca Azure günlük akışı için geçerlidir.

Uygulama mesajlarını görüntülemek için **Günlük Akışı** sayfasına gidin. Arayüz üzerinden `ILogger` uygulama tarafından kaydedilirler.

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights izleme günlüğü

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi Azure Application Insights'a günlük ler yazar. Application Insights, bir web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir. Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve çözümleyebilirsiniz.

Günlük sağlayıcısı [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)bir bağımlılık olarak ASP.NET Core için tüm kullanılabilir telemetri sağlayan bir paket olarak dahildir. Bu paketi kullanıyorsanız, sağlayıcı paketini yüklemeniz gerekmez.

ASP.NET [4.x için microsoft.applicationinsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Application Insights'a genel bakış](/azure/application-insights/app-insights-overview)
* [ASP.NET Core uygulamaları için Uygulama Öngörüleri](/azure/azure-monitor/app/asp-net-core) - Günlük le birlikte tüm Uygulama Öngörüleri telemetrisini uygulamak istiyorsanız buradan başlayın.
* [.NET Core ILogger günlükleri için ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - Application Insights telemetri geri kalanı olmadan günlük sağlayıcı uygulamak istiyorsanız buraya başlayın.
* [Uygulama Insights günlük bağdaştırıcıları](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* Microsoft Learn sitesindeki Uygulama Öngörüleri SDK - Etkileşimli öğreticiyi [yükleyin, yapılandırın ve başlayın.](/learn/modules/instrument-web-app-code-with-application-insights)

## <a name="third-party-logging-providers"></a>Üçüncü taraf günlük sağlayıcıları

ASP.NET Core ile çalışan üçüncü taraf günlük çerçeveleri:

* [elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))
* [Nöbetçi](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))

Bazı üçüncü taraf [çerçeveleri, yapılandırılmış günlük olarak da bilinen anlamsal günlük](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)gerçekleştirebilirsiniz.

Üçüncü taraf çerçevesi kullanmak, yerleşik sağlayıcılardan birini kullanmaya benzer:

1. Projenize bir NuGet paketi ekleyin.
1. Günlük `ILoggerFactory` çerçevesi tarafından sağlanan bir uzantı yöntemi çağırın.

Daha fazla bilgi için her sağlayıcının belgelerine bakın. Üçüncü taraf günlük sağlayıcıları Microsoft tarafından desteklenmez.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
