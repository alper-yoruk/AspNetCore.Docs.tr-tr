---
title: .NET Core ve ASP.NET Core oturum açma
author: rick-anderson
description: Microsoft. Extensions. Logging NuGet paketi tarafından sunulan günlüğe kaydetme çerçevesini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: ca62e374c6031ca3c2d438df87f2d13636d9c612
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776109"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core ve ASP.NET Core oturum açma

::: moniker range=">= aspnetcore-3.0"

[Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/) tarafından

.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler. Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.

Bu makalede gösterilen kod örneklerinin çoğu ASP.NET Core uygulamalardan oluşur. Bu kod parçacıklarının günlüğe kaydetmeye özgü bölümleri, [genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanan tüm .NET Core uygulamaları için geçerlidir. Genel konağın Web 'de olmayan bir uygulamada nasıl kullanılacağına ilişkin bir örnek için, [arka plan görevlerinin örnek uygulaması](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>) *program.cs* dosyasına bakın.

Genel ana bilgisayarı olmayan uygulamalar için günlük kodu, [sağlayıcıların Eklenme](#add-providers) ve [günlükçülerin oluşturulma](#create-logs)biçiminde farklılık gösterir. Ana bilgisayar olmayan kod örnekleri, makalenin bu bölümlerinde gösterilmiştir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Sağlayıcı Ekle

Günlük sağlayıcısı günlükleri görüntüler veya depolar. Örneğin, konsol sağlayıcısı günlükleri konsolunda görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Application Insights depolar. Birden çok sağlayıcı eklenerek Günlükler birden çok hedefe gönderilebilir.

Genel ana bilgisayar kullanan bir uygulamaya sağlayıcı eklemek için, `Add{provider name}` *program.cs*' de sağlayıcının uzantı yöntemini çağırın:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

Konak olmayan bir konsol uygulamasında, oluşturma sırasında sağlayıcının `Add{provider name}` uzantı yöntemini çağırın: `LoggerFactory`

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory`ve `AddConsole` için `Microsoft.Extensions.Logging`bir `using` ifade gerektirir.

Aşağıdaki günlük sağlayıcılarını ekleyen varsayılan ASP.NET Core <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>proje şablonları çağrısı:

* [Konsol](#console-provider)
* [H](#debug-provider)
* [EventSource](#event-source-provider)
* [Olay günlüğü](#windows-eventlog-provider) (yalnızca Windows üzerinde çalışırken)

Varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz. Öğesini <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>çağırın ve istediğiniz sağlayıcıları ekleyin.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

Makalenin ilerleyen kısımlarında [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve [üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.

## <a name="create-logs"></a>Günlükleri oluştur

Günlükler oluşturmak için bir <xref:Microsoft.Extensions.Logging.ILogger%601> nesnesi kullanın. Bir Web uygulamasında veya barındırılan hizmette, bir `ILogger` bağımlılık ekleme (dı) kaynağından yararlanın. Konak dışı konsol uygulamalarında, `LoggerFactory` oluşturmak için öğesini kullanın. `ILogger`

Aşağıdaki ASP.NET Core örnek, kategorisi olarak içeren `TodoApiSample.Pages.AboutModel` bir günlükçü oluşturur. Günlük *kategorisi* , her günlük ile ilişkili bir dizedir. Dı `ILogger<T>` tarafından belirtilen örnek, kategori `T` olarak tam nitelikli adı olan Günlükler oluşturur. 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Aşağıdaki konak olmayan konsol uygulaması örneği, kategorisi `LoggingConsoleApp.Program` olarak bir günlükçü oluşturur.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, günlükçü, düzeyi olan `Information` Günlükler oluşturmak için kullanılır. Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

[Düzeyler](#log-level) ve [Kategoriler](#log-category) Bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.

### <a name="create-logs-in-the-program-class"></a>Program sınıfında Günlükler oluşturma

Günlükleri bir ASP.NET Core uygulamasının `Program` sınıfına yazmak için, konak oluşturulduktan sonra bir `ILogger` örnek alın:

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez. Ancak, ayrı bir günlükçü kullanılabilir. Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateHostBuilder`. `AddSerilog`, içinde `Log.Logger`belirtilen statik yapılandırmayı kullanır:

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

### <a name="create-logs-in-the-startup-class"></a>Başlangıç sınıfında Günlükler oluşturma

Günlükleri ASP.NET Core bir uygulama `Startup.Configure` yönteminde yazmak için, yöntem imzasına bir `ILogger` parametre ekleyin:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

`Startup.ConfigureServices` Yöntemde dı kapsayıcı kurulumu tamamlanmadan önce Günlüklerin yazılması desteklenmez:

* `Startup` Oluşturucuya günlükçü ekleme desteklenmiyor.
* `Startup.ConfigureServices` Yöntem imzasına günlükçü ekleme desteklenmiyor

Bu kısıtlamanın nedeni, günlük kaydının dı ve yapılandırmaya göre değişir ve bu da, ara ' ya bağlıdır. Dı kapsayıcısı bitene kadar `ConfigureServices` ayarlanamaz.

Web ana bilgisayarı için ayrı bir `Startup` dı kapsayıcısı oluşturulduğundan, bir günlükçü 'nin önceki ASP.NET Core sürümlerinde çalışacak şekilde bir günlükçü ekleme. Genel ana bilgisayar için yalnızca bir kapsayıcı oluşturma hakkında daha fazla bilgi için bkz. [Son değişiklik duyurusu](https://github.com/aspnet/Announcements/issues/353).

`ILogger<T>`' A bağımlı olan bir hizmeti yapılandırmanız gerekiyorsa, bunu Oluşturucu ekleme veya bir fabrika yöntemi sağlayarak de yapabilirsiniz. Fabrika yöntemi yaklaşımı yalnızca başka bir seçenek yoksa önerilir. Örneğin, bir özelliği kaynağından bir hizmete doldurmanız gerektiğini varsayalım:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Önceki vurgulanmış kod, ilk kez `Func` dı kapsayıcısının bir örneğini oluşturmak için gereken bir ' dır `MyService`. Kayıtlı hizmetlerden herhangi birine bu şekilde erişebilirsiniz.

### <a name="create-logs-in-blazor"></a>Blazor 'te günlük oluşturma

#### <a name="blazor-webassembly"></a>Blazor WebAssembly

Blazor WebAssembly uygulamalarında oturum açmayı, `WebAssemblyHostBuilder.Logging` içindeki `Program.Main`özelliği ile yapılandırın:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging` Özelliği <xref:Microsoft.Extensions.Logging.ILoggingBuilder>türündedir, bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde `Logging`de kullanılabilir.

#### <a name="log-in-razor-components"></a>Razor bileşenlerinde oturum açma

Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.

İçin `using` <xref:Microsoft.Extensions.Logging> olan yönergesi, <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> ve <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>gibi API 'leri için IntelliSense 'i desteklemek için gereklidir.

Aşağıdaki örnek, Razor bileşenleri <xref:Microsoft.Extensions.Logging.ILogger> içindeki günlüğü gösterir:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

Aşağıdaki örnek, Razor bileşenleri <xref:Microsoft.Extensions.Logging.ILoggerFactory> içindeki günlüğü gösterir:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>Zaman uyumsuz günlükçü yöntemi yok

Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır. Günlüğe kaydetme veri depoluizin yavaşsa, doğrudan buna yazmayın. Başlangıç olarak günlük iletilerini hızlı bir mağazaya yazmayı ve sonra yavaş depoya daha sonra taşımayı düşünün. Örneğin, SQL Server için oturum açıyorsanız `Log` `Log` Yöntemler zaman uyumlu olduğundan, bunu doğrudan bir yöntemde yapmak istemezsiniz. Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin. Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.

## <a name="configuration"></a>Yapılandırma

Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:

* Dosya biçimleri (ıNı, JSON ve XML).
* Komut satırı bağımsız değişkenleri.
* Ortam değişkenleri.
* Bellek içi .NET nesneleri.
* Şifrelenmemiş [gizli dizi Yöneticisi](xref:security/app-secrets) depolaması.
* [Azure Key Vault](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.
* Özel sağlayıcılar (yüklü veya oluşturulmuş).

Örneğin, günlük yapılandırma genellikle uygulama ayarları dosyalarının `Logging` bölümü tarafından sağlanır. Aşağıdaki örnek tipik bir appSettings 'in içeriğini gösterir *. Development. JSON* dosyası:

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

Özelliği `Logging` , ve günlük `LogLevel` sağlayıcısı özelliklerine sahip olabilir (konsol gösterilir).

Altındaki `LogLevel` `Logging` özelliği, Seçili kategoriler için günlüğe kaydedilecek minimum [düzeyi](#log-level) belirtir. Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe kaydedilir ve diğer tüm diğerleri `Debug` düzeyinde günlüğe kaydedilir.

Günlük sağlayıcılarını belirt `Logging` altındaki diğer özellikler. Örnek, konsol sağlayıcısına yöneliktir. Bir sağlayıcı, [günlük kapsamlarını](#log-scopes)destekliyorsa, `IncludeScopes` etkinleştirilip etkinleştirilmeyeceğini belirtir. Bir sağlayıcı özelliği (örnekte olduğu `Console` gibi), bir `LogLevel` özellik de belirtebilir. `LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir.

' De `Logging.{providername}.LogLevel`düzeyler belirtilirse, içinde `Logging.LogLevel`ayarlanan her şeyi geçersiz kılar.

Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez. Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir. Örneğin, ayar dosyalarını okumak için tarafından `CreateDefaultBuilder` eklenen [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider), günlük yapılandırmasını varsayılan olarak yeniden yükler. Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.

Yapılandırma sağlayıcılarını uygulama hakkında daha fazla bilgi için <xref:fundamentals/configuration/index>bkz..

## <a name="sample-logging-output"></a>Örnek günlüğe kaydetme çıkışı

Yukarıdaki bölümde gösterilen örnek kodla, uygulama komut satırından çalıştırıldığında Günlükler konsolunda görüntülenir. Konsol çıkışının bir örneği aşağıda verilmiştir:

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

Yukarıdaki Günlükler, üzerinde `http://localhost:5000/api/todo/0`örnek uygulamaya bir http get isteği yapılarak oluşturulmuştur.

Visual Studio 'da örnek uygulamayı çalıştırdığınızda hata ayıklama penceresinde göründükleri günlüklere yönelik bir örnek aşağıda verilmiştir:

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

Yukarıdaki bölümde gösterilen `ILogger` çağrılar tarafından oluşturulan Günlükler "TodoApiSample" ile başlar. "Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır. ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.

Bu makalenin geri kalanında günlüğe kaydetme için bazı ayrıntılar ve seçenekler açıklanmaktadır.

## <a name="nuget-packages"></a>NuGet paketleri

`ILogger` Ve `ILoggerFactory` arabirimleri [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde ve bu uygulamalar için varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)içinde bulunur.

## <a name="log-category"></a>Günlük kategorisi

Bir `ILogger` nesne oluşturulduğunda, için bir *Kategori* belirtilir. Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger`. Kategori herhangi bir dize olabilir, ancak kural, "TodoApi. Controllers. TodoController" gibi sınıf adını kullanmaktır.

Kategori `ILogger<T>` `T` olarak tam nitelikli `ILogger` tür adını kullanan bir örnek almak için kullanın:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`, tam nitelikli tür `CreateLogger` adı ile çağırma ile eşdeğerdir `T`.

## <a name="log-level"></a>Günlük düzeyi

Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir. Günlük düzeyi önem derecesini veya önemini gösterir. Örneğin, bir yöntem normal olarak sona `Information` erdiğinde ve `Warning` bir yöntem *404* bulunmayan bir durum kodu döndürdüğünde bir günlük yazabilirsiniz.

Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Yukarıdaki kodda, ilk parametre [günlük olay kimliğidir](#log-event-id). İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur. Yöntem parametreleri bu makalenin ilerleyen kısımlarında bulunan [ileti şablonu bölümünde](#log-message-template) açıklanmaktadır.

Yöntem adında düzeyi (örneğin, `LogInformation` ve `LogWarning`) Içeren günlük yöntemleri, [ILogger için uzantı yöntemleridir](xref:Microsoft.Extensions.Logging.LoggerExtensions). Bu yöntemler bir `LogLevel` parametre `Log` alan bir yöntemi çağırır. `Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak söz dizimi görece karmaşıktır. Daha fazla bilgi için bkz <xref:Microsoft.Extensions.Logging.ILogger> . ve [günlükçü uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core, en küçükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.

* İzleme = 0

  Genellikle yalnızca hata ayıklama için değerli bilgiler için. Bu iletiler hassas uygulama verileri içerebilir, bu nedenle bir üretim ortamında etkinleştirilmemelidir. *Varsayılan olarak devre dışıdır.*

* Hata Ayıkla = 1

  Geliştirme ve hata ayıklama konusunda yararlı olabilecek bilgiler için. Örnek: `Entering method Configure with flag set to true.` yüksek `Debug` günlük hacimden dolayı yalnızca sorun giderirken üretim ortamında düzey günlüklerini etkinleştirin.

* Bilgi = 2

  Uygulamanın genel akışını izlemek için. Bu günlüklerde genellikle uzun süreli bir değer vardır. Örnek: `Request received for path /api/todo`

* Uyarı = 3

  Uygulama akışında anormal veya beklenmedik olaylar için. Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gerekebilecek hataları veya diğer koşulları içerebilir. İşlenmiş özel durumlar, `Warning` günlük düzeyini kullanmak için yaygın bir yerdir. Örnek: `FileNotFoundException for file quotes.txt.`

* Hata = 4

  İşlenemeyen hatalar ve özel durumlar için. Bu iletiler, uygulama genelinde bir hata değil geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir. Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`

* Kritik = 5

  Anında ilgilenilmesi gereken hatalarda. Örnekler: veri kaybı senaryoları, disk alanı yetersiz.

Belirli bir depolama ortamında veya görüntüleme penceresinde ne kadar günlük çıkışının yazıldığını denetlemek için günlük düzeyini kullanın. Örneğin:

* Üretimde:
  * Geçiş `Information` düzeyinde günlüğe `Trace` kaydetme, yüksek hacimli ayrıntılı günlük iletileri oluşturur. Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, düzey `Trace` iletiler `Information` üzerinden yüksek hacimli, düşük maliyetli bir veri deposuna oturum açın.
  * `Warning` Düzeylerde oturum açma işlemi genellikle daha az, daha küçük günlük iletileri `Critical` üretir. Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir ve bu da veri deposu seçiminden daha fazla esneklik elde etmez.
* Geliştirme sırasında:
  * Konsola `Warning` iletiler `Critical` aracılığıyla oturum açın.
  * Sorun `Trace` giderirken `Information` iletiler aracılığıyla ekleyin.

Bu makalede daha sonra bulunan [günlük filtreleme](#log-filtering) bölümünde, bir sağlayıcının hangi günlük düzeylerinin işlediğini nasıl denetleneceği açıklanmaktadır.

ASP.NET Core çerçeve olayları için günlükleri yazar. Bu makalenin önceki kısımlarında yer alınan günlük örnekleri, günlüklerin `Information` altında tutulur, bu `Debug` nedenle `Trace` hiçbir veya düzeyi günlük oluşturulmaz. Günlükleri göstermek `Debug` için yapılandırılmış örnek uygulama çalıştırılarak oluşturulan konsol günlüklerinin bir örneği aşağıda verilmiştir:

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

## <a name="log-event-id"></a>Günlüğe olay KIMLIĞI

Her günlük bir *olay kimliği*belirtebilir. Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

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

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

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

Günlükleri Azure Tablo depolama alanına gönderiyorsanız, her bir Azure Tablo varlığı, günlük verilerinde sorguları basitleştiren `ID` ve `RequestTime` özelliklere sahip olabilir. Bir sorgu, belirli `RequestTime` bir aralıktaki tüm günlükleri, kısa mesajdan zaman aşımını ayrıştırmadan bulabilir.

## <a name="logging-exceptions"></a>Günlüğe kaydetme özel durumları

Günlükçü yöntemlerinin, aşağıdaki örnekte olduğu gibi bir özel durum iletmenizi sağlayan aşırı yüklemeleri vardır:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Özel durum bilgileri farklı yollarla farklı sağlayıcılarda işler. Yukarıda gösterilen koddan hata ayıklama sağlayıcısı çıktısına bir örnek aşağıda verilmiştir.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Günlük filtreleme

Belirli bir sağlayıcı ve kategori için en az bir günlük düzeyi veya tüm sağlayıcılar ya da tüm kategoriler için belirtebilirsiniz. Minimum düzeyin altındaki tüm Günlükler bu sağlayıcıya aktarılmaz, bu nedenle görüntülenmez veya depolanmaz.

Tüm günlükleri gizlemek için en düşük `LogLevel.None` günlük düzeyi olarak belirtin. Öğesinin `LogLevel.None` tamsayı değeri 6 ' dır `LogLevel.Critical` (5) daha yüksektir.

### <a name="create-filter-rules-in-configuration"></a>Yapılandırmada filtre kuralları oluşturma

Proje şablonu kodu, konsol `CreateDefaultBuilder` , hata ayıklama ve EventSource (ASP.NET Core 2,2 veya üzeri) sağlayıcılar için günlük kaydı ayarlamak için çağırır. `CreateDefaultBuilder` Yöntemi, [Bu makalenin önceki kısımlarında](#configuration)açıklandığı gibi, bir `Logging` bölümde yapılandırmayı aramak için günlük kaydı yapar.

Yapılandırma verileri aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en düşük günlük düzeylerini belirtir:

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

Bu JSON altı filtre kuralı oluşturur: biri hata ayıklama sağlayıcısı, konsol sağlayıcısı için dört ve diğeri tüm sağlayıcılar için. Bir `ILogger` nesne oluşturulduğunda her sağlayıcı için tek bir kural seçilir.

### <a name="filter-rules-in-code"></a>Koddaki filtre kuralları

Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

İkincisi `AddFilter` , hata ayıklama sağlayıcısını tür adını kullanarak belirtir. Birincisi `AddFilter` bir sağlayıcı türü belirtmediğinden, tüm sağlayıcılar için geçerlidir.

### <a name="how-filtering-rules-are-applied"></a>Filtreleme kuralları nasıl uygulanır

Yapılandırma verileri ve önceki örneklerde `AddFilter` gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur. İlk altı yapılandırma örneğinde ve son iki ise kod örneğinde gelir.

| Sayı | Sağlayıcı      | Şununla başlayan Kategoriler...          | En düşük günlük düzeyi |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Hata ayıklama         | Tüm kategoriler                          | Bilgi       |
| 2      | Konsol       | Microsoft. AspNetCore. Mvc. Razor. Internal | Uyarı           |
| 3      | Konsol       | Microsoft. AspNetCore. Mvc. Razor. Razor    | Hata ayıklama             |
| 4      | Konsol       | Microsoft. AspNetCore. Mvc. Razor          | Hata             |
| 5      | Konsol       | Tüm kategoriler                          | Bilgi       |
| 6      | Tüm sağlayıcılar | Tüm kategoriler                          | Hata ayıklama             |
| 7      | Tüm sağlayıcılar | Sistem                                  | Hata ayıklama             |
| 8      | Hata ayıklama         | Microsoft                               | İzleme             |

Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer. Bir `ILogger` örnek tarafından yazılan tüm iletiler, seçilen kurallara göre filtrelenmiştir. Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.

Belirli bir kategori için oluşturulan her sağlayıcı `ILogger` için aşağıdaki algoritma kullanılır:

* Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin. Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.
* Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin. Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.
* Birden çok kural seçilirse, **son** olanı götürün.
* Hiçbir kural seçilmezse, kullanın `MinimumLevel`.

Yukarıdaki kurallar listesinde, "Microsoft. AspNetCore. Mvc `ILogger` . Razor. RazorViewEngine" kategorisi için bir nesne oluşturduğunuzu varsayalım:

* Hata ayıklama sağlayıcısı, kurallar 1, 6 ve 8 için geçerlidir. Kural 8 ' i en özeldir, yani seçili olanı seçilidir.
* Konsol sağlayıcısı için, kurallar 3, 4, 5 ve 6 geçerlidir. Kural 3 en özeldir.

Elde edilen `ILogger` örnek, hata ayıklama `Trace` sağlayıcısına düzeyi ve üzeri Günlükler gönderir. `Debug` Düzeyi ve üzeri Günlükler konsol sağlayıcısına gönderilir.

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

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

En düşük düzeyi açıkça ayarlamazsanız, varsayılan değer olur `Information`ve `Trace` `Debug` bu, günlüklerin yok sayılacağı anlamına gelir.

### <a name="filter-functions"></a>Filtre işlevleri

Configuration veya Code tarafından kendisine atanmış kuralları olmayan tüm sağlayıcılar ve kategoriler için bir filtre işlevi çağırılır. İşlevindeki kodun sağlayıcı türü, kategorisi ve günlük düzeyine erişimi vardır. Örneğin:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a>Sistem kategorileri ve Düzeyler

ASP.NET Core ve Entity Framework Core tarafından kullanılan bazı kategoriler şunlardır ve bunlardan beklenen Günlükler hakkında notlar bulunur:

| Kategori                            | Notlar |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Genel ASP.NET Core tanılama. |
| Microsoft.AspNetCore.DataProtection | Hangi anahtarların kabul edildiği, bulunduğu ve kullanıldığı. |
| Microsoft.AspNetCore.HostFiltering  | İzin verilen konaklar. |
| Microsoft.AspNetCore.Hosting        | HTTP isteklerinin tamamlanması için geçen süre ve ne zaman başladıkları. Hangi barındırma başlangıç derlemeleri yüklendi. |
| Microsoft.AspNetCore.Mvc            | MVC ve Razor tanılama. Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi. |
| Microsoft.AspNetCore.Routing        | Eşleşen bilgileri yönlendirin. |
| Microsoft. AspNetCore. Server         | Bağlantı başlatın, durdurun ve canlı yanıtları koruyun. HTTPS sertifika bilgileri. |
| Microsoft.AspNetCore.StaticFiles    | Sunulan dosyalar. |
| Microsoft. EntityFrameworkCore       | Genel Entity Framework Core tanılama. Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler. |

## <a name="log-scopes"></a>Günlük kapsamları

 *Kapsam* bir mantıksal işlemler kümesini gruplandırabilir. Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir. Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.

Kapsam, <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemi tarafından `IDisposable` döndürülen ve atılana kadar sürer olan bir türdür. Bir `using` blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:

*Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol günlükçü seçeneğinin yapılandırılması gerekir.
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
* [H](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Stdout ve ASP.NET Core modüllü hata ayıklama günlüğü hakkında bilgi için bkz <xref:test/troubleshoot-azure-iis> . ve <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

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

[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını (`Debug.WriteLine` Yöntem çağrıları) kullanarak günlük çıktısını yazar.

Linux 'ta, bu sağlayıcı günlükleri */var/log/Message*dosyasına yazar.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Olay kaynak sağlayıcısı

[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi, adıyla `Microsoft-Extensions-Logging`bir olay kaynağı çapraz platformuna yazar. Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.

```csharp
logging.AddEventSourceLogger();
```

Olay kaynak sağlayıcısı, konağı oluşturmak için çağrıldığında `CreateDefaultBuilder` otomatik olarak eklenir.

#### <a name="dotnet-trace-tooling"></a>DotNet izleme araçları

[DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace) Aracı, çalışan bir Işlemin .NET Core izlemelerinin toplanmasını sağlayan platformlar arası CLI genel aracıdır. Araç, kullanarak <xref:Microsoft.Extensions.Logging.EventSource> sağlayıcı verilerini toplar <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

DotNet Trace araçları komutunu aşağıdaki komutla birlikte yüklersiniz:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Bir uygulamadan izleme toplamak için DotNet Trace araçları kullanın:

1. Uygulama Konağı ile `CreateDefaultBuilder`derlenmezse, [olay kaynak sağlayıcısını](#event-source-provider) uygulamanın günlük yapılandırmasına ekleyin.

1. `dotnet run` Komutuyla uygulamayı çalıştırın.

1. .NET Core uygulamasının işlem tanımlayıcısını (PID) belirleme:

   * Windows 'ta aşağıdaki yaklaşımlardan birini kullanın:
     * Görev Yöneticisi (Ctrl + Alt + Del)
     * [Tasklist komutu](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process PowerShell komutu](/powershell/module/microsoft.powershell.management/get-process)
   * Linux 'ta [pidof komutunu](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)kullanın.

   Uygulamanın derlemesi ile aynı ada sahip olan işlem için PID 'i bulun.

1. `dotnet trace` Komutunu yürütün.

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

   PowerShell komut kabuğu kullanırken, `--providers` değeri tek tırnak işaretleri (`'`) içine alın:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Windows dışı platformlarda, çıkış izleme dosyasının biçimini `-f speedscope` olarak `speedscope`değiştirme seçeneğini ekleyin.

   | Sözcükle | Açıklama |
   | :-----: | ----------- |
   | 1       | İçin `LoggingEventSource`meta olayları günlüğe kaydedin. Olayları günlüğe kaydetmez `ILogger`). |
   | 2       | Çağrıldığında `Message` olayı `ILogger.Log()` açar. Programlı (biçimlendirilmedi) bir şekilde bilgi sağlar. |
   | 4       | Çağrıldığında `FormatMessage` olayı `ILogger.Log()` açar. , Bilgilerin biçimlendirilen dize sürümünü sağlar. |
   | 8       | Çağrıldığında `MessageJson` olayı `ILogger.Log()` açar. Bağımsız değişkenlerin JSON gösterimini sağlar. |

   | Olay düzeyi | Açıklama     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`için `{Logger Category}` girdiler ve `{Event Level}` ek günlük filtreleme koşullarını temsil eder. Girişleri `FilterSpecs` noktalı virgül (`;`) ile ayırın.

   Bir Windows komut kabuğu ( `--providers` değer etrafında tek tırnak**olmadan** ) ile örnek:

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Yukarıdaki komut şunları etkinleştirir:

   * Hatalar (`4``2`) için biçimlendirilen dizeler () üretmek üzere olay kaynağı günlükçüsü.
   * `Microsoft.AspNetCore.Hosting``Informational` günlüğe kaydetme düzeyinde (`4`) günlüğe kaydetme.

1. ENTER tuşuna veya CTRL + C tuşlarına basarak DotNet izleme araçlarını durdurun.

   İzleme, `dotnet trace` komutun yürütüldüğü klasörde *Trace. NetTrace* adıyla kaydedilir.

1. Trace 'i [PerfView](#perfview)ile açın. *Trace. NetTrace* dosyasını açın ve izleme olaylarını araştırın.

Daha fazla bilgi için bkz.

* [Performans Analizi yardımcı programı Için izleme (DotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core belgeleri)
* [Performans Analizi yardımcı programı (DotNet-Trace) Için izleme](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (DotNet/Diagnostics GitHub deposu belgeleri)
* [Loggingeventsource sınıfı](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API tarayıcısı)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Loggingeventsource başvuru kaynağı (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; farklı bir sürüme yönelik başvuru kaynağını almak için dalı olarak `release/{Version}`değiştirin, burada `{Version}` ASP.NET Core sürümü istenen sürümdür.
* [PerfView](#perfview) &ndash; olay kaynağı izlemelerini görüntülemek için kullanışlıdır.

#### <a name="perfview"></a>PerfView

Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın. ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.

Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin. (Dizenin başlangıcında yıldız işaretini kaçırmayın.)

![PerfView ek sağlayıcıları](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows olay günlüğü sağlayıcısı

[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi, Windows olay günlüğüne günlük çıktısı gönderir.

```csharp
logging.AddEventLog();
```

[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) , geçiş yapmanızı <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>sağlar. `null` Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:

* `LogName`&ndash; "Uygulama"
* `SourceName`&ndash; ".NET çalışma zamanı"
* `MachineName`&ndash; yerel makine

Olaylar, [Uyarı düzeyi ve üzeri](#log-level)için günlüğe kaydedilir. Olayları daha düşük günlüğe kaydetmek `Warning`için, günlük düzeyini açık olarak ayarlayın. Örneğin, *appSettings. JSON* dosyasına aşağıdakini ekleyin:

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>TraceSource sağlayıcısı

[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıklarını ve sağlayıcıları kullanır.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Addtracesource aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) , bir kaynak anahtarı ve bir izleme dinleyicisi geçirmenize olanak sağlar.

Bu sağlayıcıyı kullanmak için, bir uygulamanın .NET Framework çalışması gerekir (.NET Core yerine). Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan gibi çeşitli [dinleyicilerine](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.

### <a name="azure-app-service-provider"></a>Azure App Service sağlayıcı

[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Sağlayıcı paketi, paylaşılan çerçeveye dahil değildir. Sağlayıcıyı kullanmak için sağlayıcı paketini projeye ekleyin.

Sağlayıcı ayarlarını yapılandırmak için aşağıdaki örnekte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> gösterildiği <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>gibi ve kullanın:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

App Service uygulamasına dağıtırken, uygulama, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarları kabul eder. Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.

* **Uygulama günlüğü (dosya sistemi)**
* **Uygulama günlüğü (blob)**

Günlük dosyaları için varsayılan konum *D:\\Home\\LogFiles\\uygulama* klasöründedir ve varsayılan dosya adı *Diagnostics-YYYYMMDD. txt*' dir. Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir. Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid}-ApplicationLog.txt*.

Sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında çalışır. Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya blob 'lar için yerel geliştirme depolamasına yazmazsa, bu, hiçbir etkiye sahip değildir.

#### <a name="azure-log-streaming"></a>Azure günlük akışı

Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemenize izin verir:

* Uygulama sunucusu
* Web sunucusu
* Başarısız istek izleme

Azure günlük akışını yapılandırmak için:

* Uygulamanızın Portal sayfasından **App Service günlükleri** sayfasına gidin.
* **Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.
* Günlük **düzeyini**seçin. Bu ayar, uygulamadaki diğer günlük sağlayıcılarını değil, yalnızca Azure günlük akışı için geçerlidir.

Uygulama iletilerini görüntülemek için **günlük akışı** sayfasına gidin. Uygulama tarafından `ILogger` arabirim aracılığıyla günlüğe kaydedilir.

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights izleme günlüğü

[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri Azure Application Insights yazar. Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir. Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.

Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir. Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.

ASP.NET 4. x için [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.

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
1. Günlüğe kaydetme `ILoggerFactory` çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.

Daha fazla bilgi için bkz. her sağlayıcının belgeleri. Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Tom Dykstra](https://github.com/tdykstra) ve [Steve Smith](https://ardalis.com/) tarafından

.NET Core, çeşitli yerleşik ve üçüncü taraf oturum açma sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler. Bu makalede, yerleşik sağlayıcılarla günlüğe kaydetme API 'sinin nasıl kullanılacağı gösterilmektedir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Sağlayıcı Ekle

Günlük sağlayıcısı günlükleri görüntüler veya depolar. Örneğin, konsol sağlayıcısı günlükleri konsolunda görüntüler ve Azure Application Insights sağlayıcısı bunları Azure Application Insights depolar. Birden çok sağlayıcı eklenerek Günlükler birden çok hedefe gönderilebilir.

Bir sağlayıcı eklemek için sağlayıcının `Add{provider name}` uzantı yöntemini *program.cs*içinde çağırın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Yukarıdaki kod, ve `Microsoft.Extensions.Logging` `Microsoft.Extensions.Configuration`için başvuruları gerektirir.

Varsayılan proje şablonu, aşağıdaki <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>günlük sağlayıcılarını ekleyen çağırır:

* Konsol
* Hata ayıklama
* EventSource (ASP.NET Core 2,2 ' den başlayarak)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Kullanıyorsanız `CreateDefaultBuilder`, varsayılan sağlayıcıları kendi seçimlerinizle değiştirebilirsiniz. Öğesini <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>çağırın ve istediğiniz sağlayıcıları ekleyin.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Makalenin ilerleyen kısımlarında [yerleşik günlük sağlayıcıları](#built-in-logging-providers) ve [üçüncü taraf günlüğü sağlayıcıları](#third-party-logging-providers) hakkında daha fazla bilgi edinin.

## <a name="create-logs"></a>Günlükleri oluştur

Günlükler oluşturmak için bir <xref:Microsoft.Extensions.Logging.ILogger%601> nesnesi kullanın. Bir Web uygulamasında veya barındırılan hizmette, bir `ILogger` bağımlılık ekleme (dı) kaynağından yararlanın. Konak dışı konsol uygulamalarında, `LoggerFactory` oluşturmak için öğesini kullanın. `ILogger`

Aşağıdaki ASP.NET Core örnek, kategorisi olarak içeren `TodoApiSample.Pages.AboutModel` bir günlükçü oluşturur. Günlük *kategorisi* , her günlük ile ilişkili bir dizedir. Dı `ILogger<T>` tarafından belirtilen örnek, kategori `T` olarak tam nitelikli adı olan Günlükler oluşturur. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Aşağıdaki ASP.NET Core ve konsol uygulaması örneklerinde, günlükçü, düzeyi olan `Information` Günlükler oluşturmak için kullanılır. Günlük *düzeyi* günlüğe kaydedilen etkinliğin önem derecesini gösterir.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Düzeyler](#log-level) ve [Kategoriler](#log-category) Bu makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır.

### <a name="create-logs-in-startup"></a>Başlangıçta günlük oluşturma

`Startup` Sınıf içindeki günlükleri yazmak için, Oluşturucu imzasına bir `ILogger` parametre ekleyin:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Program sınıfında Günlükler oluşturma

`Program` Sınıf içindeki günlükleri yazmak IÇIN, dı 'den bir `ILogger` örnek alın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Ana bilgisayar oluşturma sırasında günlüğe kaydetme doğrudan desteklenmez. Ancak, ayrı bir günlükçü kullanılabilir. Aşağıdaki örnekte, oturum açmak için bir [Serilog](https://serilog.net/) günlükçüsü kullanılır `CreateWebHostBuilder`. `AddSerilog`, içinde `Log.Logger`belirtilen statik yapılandırmayı kullanır:

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

Günlüğe kaydetme, zaman uyumsuz kodun performans maliyetine değer olmaması kadar hızlı olmalıdır. Günlüğe kaydetme veri depoluizin yavaşsa, doğrudan buna yazmayın. Başlangıç olarak günlük iletilerini hızlı bir mağazaya yazmayı ve sonra yavaş depoya daha sonra taşımayı düşünün. Örneğin, SQL Server için oturum açıyorsanız `Log` `Log` Yöntemler zaman uyumlu olduğundan, bunu doğrudan bir yöntemde yapmak istemezsiniz. Bunun yerine, günlük iletilerini bir bellek içi kuyruğa eşzamanlı olarak ekleyin ve bir arka plan çalışanı, SQL Server veri gönderme zaman uyumsuz çalışmasını sağlamak için iletileri kuyruktan çekin. Daha fazla bilgi için [Bu](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub sorununa bakın.

## <a name="configuration"></a>Yapılandırma

Günlüğe kaydetme sağlayıcısı yapılandırması bir veya daha fazla yapılandırma sağlayıcısı tarafından sağlanır:

* Dosya biçimleri (ıNı, JSON ve XML).
* Komut satırı bağımsız değişkenleri.
* Ortam değişkenleri.
* Bellek içi .NET nesneleri.
* Şifrelenmemiş [gizli dizi Yöneticisi](xref:security/app-secrets) depolaması.
* [Azure Key Vault](xref:security/key-vault-configuration)gibi şifreli bir kullanıcı deposu.
* Özel sağlayıcılar (yüklü veya oluşturulmuş).

Örneğin, günlük yapılandırma genellikle uygulama ayarları dosyalarının `Logging` bölümü tarafından sağlanır. Aşağıdaki örnek tipik bir appSettings 'in içeriğini gösterir *. Development. JSON* dosyası:

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

Özelliği `Logging` , ve günlük `LogLevel` sağlayıcısı özelliklerine sahip olabilir (konsol gösterilir).

Altındaki `LogLevel` `Logging` özelliği, Seçili kategoriler için günlüğe kaydedilecek minimum [düzeyi](#log-level) belirtir. Örnekte `System` ve `Microsoft` kategoriler `Information` düzeyinde günlüğe kaydedilir ve diğer tüm diğerleri `Debug` düzeyinde günlüğe kaydedilir.

Günlük sağlayıcılarını belirt `Logging` altındaki diğer özellikler. Örnek, konsol sağlayıcısına yöneliktir. Bir sağlayıcı, [günlük kapsamlarını](#log-scopes)destekliyorsa, `IncludeScopes` etkinleştirilip etkinleştirilmeyeceğini belirtir. Bir sağlayıcı özelliği (örnekte olduğu `Console` gibi), bir `LogLevel` özellik de belirtebilir. `LogLevel`sağlayıcı altında, bu sağlayıcının günlüğe kaydedilecek düzeyleri belirtir.

' De `Logging.{providername}.LogLevel`düzeyler belirtilirse, içinde `Logging.LogLevel`ayarlanan her şeyi geçersiz kılar.

Günlüğe kaydetme API 'SI, bir uygulama çalışırken günlük düzeylerini değiştirme senaryosu içermez. Ancak, bazı yapılandırma sağlayıcıları yapılandırmayı yeniden yükleme yeteneğine sahiptir ve bu, günlüğe kaydetme yapılandırması üzerinde etkili bir şekilde gerçekleşir. Örneğin, ayar dosyalarını okumak için tarafından `CreateDefaultBuilder` eklenen [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider), günlük yapılandırmasını varsayılan olarak yeniden yükler. Uygulama çalışırken kodda yapılandırma değiştirilirse uygulama, uygulamanın günlük yapılandırmasını güncelleştirmek için [IController. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) ' i çağırabilir.

Yapılandırma sağlayıcılarını uygulama hakkında daha fazla bilgi için <xref:fundamentals/configuration/index>bkz..

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

Yukarıdaki Günlükler, üzerinde `http://localhost:5000/api/todo/0`örnek uygulamaya bir http get isteği yapılarak oluşturulmuştur.

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

Yukarıdaki bölümde gösterilen `ILogger` çağrılar tarafından oluşturulan Günlükler "TodoApi" ile başlar. "Microsoft" kategorileri ile başlayan Günlükler ASP.NET Core Framework kodundan alınır. ASP.NET Core ve uygulama kodu aynı günlük API 'sini ve sağlayıcılarını kullanıyor.

Bu makalenin geri kalanında günlüğe kaydetme için bazı ayrıntılar ve seçenekler açıklanmaktadır.

## <a name="nuget-packages"></a>NuGet paketleri

`ILogger` Ve `ILoggerFactory` arabirimleri [Microsoft. Extensions. Logging. soyutlamalar](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)içinde ve bu uygulamalar için varsayılan uygulamalar [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)içinde bulunur.

## <a name="log-category"></a>Günlük kategorisi

Bir `ILogger` nesne oluşturulduğunda, için bir *Kategori* belirtilir. Bu kategori, bu örneği tarafından oluşturulan her günlük iletisine dahildir `ILogger`. Kategori herhangi bir dize olabilir, ancak kural, "TodoApi. Controllers. TodoController" gibi sınıf adını kullanmaktır.

Kategori `ILogger<T>` `T` olarak tam nitelikli `ILogger` tür adını kullanan bir örnek almak için kullanın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Kategoriyi açıkça belirtmek için şunu çağırın `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`, tam nitelikli tür `CreateLogger` adı ile çağırma ile eşdeğerdir `T`.

## <a name="log-level"></a>Günlük düzeyi

Her günlük bir <xref:Microsoft.Extensions.Logging.LogLevel> değer belirtir. Günlük düzeyi önem derecesini veya önemini gösterir. Örneğin, bir yöntem normal olarak sona `Information` erdiğinde ve `Warning` bir yöntem *404* bulunmayan bir durum kodu döndürdüğünde bir günlük yazabilirsiniz.

Aşağıdaki kod oluşturur `Information` ve `Warning` günlükleri:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Yukarıdaki kodda, ilk parametre [günlük olay kimliğidir](#log-event-id). İkinci parametre, kalan Yöntem parametreleri tarafından belirtilen bağımsız değişken değerleri için yer tutucuları olan bir ileti şablonudur. Yöntem parametreleri bu makalenin ilerleyen kısımlarında bulunan [ileti şablonu bölümünde](#log-message-template) açıklanmaktadır.

Yöntem adında düzeyi (örneğin, `LogInformation` ve `LogWarning`) Içeren günlük yöntemleri, [ILogger için uzantı yöntemleridir](xref:Microsoft.Extensions.Logging.LoggerExtensions). Bu yöntemler bir `LogLevel` parametre `Log` alan bir yöntemi çağırır. `Log` Yöntemi bu uzantı yöntemlerinden biri yerine doğrudan çağırabilirsiniz, ancak söz dizimi görece karmaşıktır. Daha fazla bilgi için bkz <xref:Microsoft.Extensions.Logging.ILogger> . ve [günlükçü uzantıları kaynak kodu](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core, en küçükten en yüksek öneme doğru sıralanan aşağıdaki günlük düzeylerini tanımlar.

* İzleme = 0

  Genellikle yalnızca hata ayıklama için değerli bilgiler için. Bu iletiler hassas uygulama verileri içerebilir, bu nedenle bir üretim ortamında etkinleştirilmemelidir. *Varsayılan olarak devre dışıdır.*

* Hata Ayıkla = 1

  Geliştirme ve hata ayıklama konusunda yararlı olabilecek bilgiler için. Örnek: `Entering method Configure with flag set to true.` yüksek `Debug` günlük hacimden dolayı yalnızca sorun giderirken üretim ortamında düzey günlüklerini etkinleştirin.

* Bilgi = 2

  Uygulamanın genel akışını izlemek için. Bu günlüklerde genellikle uzun süreli bir değer vardır. Örnek: `Request received for path /api/todo`

* Uyarı = 3

  Uygulama akışında anormal veya beklenmedik olaylar için. Bunlar, uygulamanın durmasına neden olmayan ancak araştırılması gerekebilecek hataları veya diğer koşulları içerebilir. İşlenmiş özel durumlar, `Warning` günlük düzeyini kullanmak için yaygın bir yerdir. Örnek: `FileNotFoundException for file quotes.txt.`

* Hata = 4

  İşlenemeyen hatalar ve özel durumlar için. Bu iletiler, uygulama genelinde bir hata değil geçerli etkinlikte veya işlemde (geçerli HTTP isteği gibi) bir hata olduğunu gösterir. Örnek günlük iletisi:`Cannot insert record due to duplicate key violation.`

* Kritik = 5

  Anında ilgilenilmesi gereken hatalarda. Örnekler: veri kaybı senaryoları, disk alanı yetersiz.

Belirli bir depolama ortamında veya görüntüleme penceresinde ne kadar günlük çıkışının yazıldığını denetlemek için günlük düzeyini kullanın. Örneğin:

* Üretimde:
  * Geçiş `Information` düzeyinde günlüğe `Trace` kaydetme, yüksek hacimli ayrıntılı günlük iletileri oluşturur. Maliyetleri denetlemek ve veri depolama sınırlarını aşmamak için, düzey `Trace` iletiler `Information` üzerinden yüksek hacimli, düşük maliyetli bir veri deposuna oturum açın.
  * `Warning` Düzeylerde oturum açma işlemi genellikle daha az, daha küçük günlük iletileri `Critical` üretir. Bu nedenle, maliyetler ve depolama sınırları genellikle bir sorun değildir ve bu da veri deposu seçiminden daha fazla esneklik elde etmez.
* Geliştirme sırasında:
  * Konsola `Warning` iletiler `Critical` aracılığıyla oturum açın.
  * Sorun `Trace` giderirken `Information` iletiler aracılığıyla ekleyin.

Bu makalede daha sonra bulunan [günlük filtreleme](#log-filtering) bölümünde, bir sağlayıcının hangi günlük düzeylerinin işlediğini nasıl denetleneceği açıklanmaktadır.

ASP.NET Core çerçeve olayları için günlükleri yazar. Bu makalenin önceki kısımlarında yer alınan günlük örnekleri, günlüklerin `Information` altında tutulur, bu `Debug` nedenle `Trace` hiçbir veya düzeyi günlük oluşturulmaz. Günlükleri göstermek `Debug` için yapılandırılmış örnek uygulama çalıştırılarak oluşturulan konsol günlüklerinin bir örneği aşağıda verilmiştir:

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

Her günlük bir *olay kimliği*belirtebilir. Örnek uygulama bunu yerel olarak tanımlanmış `LoggingEvents` bir sınıf kullanarak yapar:

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

Günlükleri Azure Tablo depolama alanına gönderiyorsanız, her bir Azure Tablo varlığı, günlük verilerinde sorguları basitleştiren `ID` ve `RequestTime` özelliklere sahip olabilir. Bir sorgu, belirli `RequestTime` bir aralıktaki tüm günlükleri, kısa mesajdan zaman aşımını ayrıştırmadan bulabilir.

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

Tüm günlükleri gizlemek için en düşük `LogLevel.None` günlük düzeyi olarak belirtin. Öğesinin `LogLevel.None` tamsayı değeri 6 ' dır `LogLevel.Critical` (5) daha yüksektir.

### <a name="create-filter-rules-in-configuration"></a>Yapılandırmada filtre kuralları oluşturma

Proje şablonu kodu, konsol `CreateDefaultBuilder` , hata ayıklama ve EventSource (ASP.NET Core 2,2 veya üzeri) sağlayıcılar için günlük kaydı ayarlamak için çağırır. `CreateDefaultBuilder` Yöntemi, [Bu makalenin önceki kısımlarında](#configuration)açıklandığı gibi, bir `Logging` bölümde yapılandırmayı aramak için günlük kaydı yapar.

Yapılandırma verileri aşağıdaki örnekte olduğu gibi sağlayıcıya ve kategoriye göre en düşük günlük düzeylerini belirtir:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Bu JSON altı filtre kuralı oluşturur: biri hata ayıklama sağlayıcısı, konsol sağlayıcısı için dört ve diğeri tüm sağlayıcılar için. Bir `ILogger` nesne oluşturulduğunda her sağlayıcı için tek bir kural seçilir.

### <a name="filter-rules-in-code"></a>Koddaki filtre kuralları

Aşağıdaki örnek, koddaki filtre kurallarının nasıl kaydedileceği gösterilmektedir:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

İkincisi `AddFilter` , hata ayıklama sağlayıcısını tür adını kullanarak belirtir. Birincisi `AddFilter` bir sağlayıcı türü belirtmediğinden, tüm sağlayıcılar için geçerlidir.

### <a name="how-filtering-rules-are-applied"></a>Filtreleme kuralları nasıl uygulanır

Yapılandırma verileri ve önceki örneklerde `AddFilter` gösterilen kod, aşağıdaki tabloda gösterilen kuralları oluşturur. İlk altı yapılandırma örneğinde ve son iki ise kod örneğinde gelir.

| Sayı | Sağlayıcı      | Şununla başlayan Kategoriler...          | En düşük günlük düzeyi |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Hata ayıklama         | Tüm kategoriler                          | Bilgi       |
| 2      | Konsol       | Microsoft. AspNetCore. Mvc. Razor. Internal | Uyarı           |
| 3      | Konsol       | Microsoft. AspNetCore. Mvc. Razor. Razor    | Hata ayıklama             |
| 4      | Konsol       | Microsoft. AspNetCore. Mvc. Razor          | Hata             |
| 5      | Konsol       | Tüm kategoriler                          | Bilgi       |
| 6      | Tüm sağlayıcılar | Tüm kategoriler                          | Hata ayıklama             |
| 7      | Tüm sağlayıcılar | Sistem                                  | Hata ayıklama             |
| 8      | Hata ayıklama         | Microsoft                               | İzleme             |

Bir `ILogger` nesne oluşturulduğunda, `ILoggerFactory` nesne bu günlükçü için uygulanacak her sağlayıcı için tek bir kural seçer. Bir `ILogger` örnek tarafından yazılan tüm iletiler, seçilen kurallara göre filtrelenmiştir. Her sağlayıcı ve kategori çifti için mümkün olan en özel kural kullanılabilir kurallardan seçilir.

Belirli bir kategori için oluşturulan her sağlayıcı `ILogger` için aşağıdaki algoritma kullanılır:

* Sağlayıcı veya diğer adıyla eşleşen tüm kuralları seçin. Hiçbir eşleşme bulunmazsa, boş bir sağlayıcıya sahip tüm kurallar ' ı seçin.
* Önceki adımın sonucunda, en uzun eşleşen kategori ön ekine sahip kurallar ' ı seçin. Eşleşme bulunmazsa, kategori belirtmeyen tüm kuralları seçin.
* Birden çok kural seçilirse, **son** olanı götürün.
* Hiçbir kural seçilmezse, kullanın `MinimumLevel`.

Yukarıdaki kurallar listesinde, "Microsoft. AspNetCore. Mvc `ILogger` . Razor. RazorViewEngine" kategorisi için bir nesne oluşturduğunuzu varsayalım:

* Hata ayıklama sağlayıcısı, kurallar 1, 6 ve 8 için geçerlidir. Kural 8 ' i en özeldir, yani seçili olanı seçilidir.
* Konsol sağlayıcısı için, kurallar 3, 4, 5 ve 6 geçerlidir. Kural 3 en özeldir.

Elde edilen `ILogger` örnek, hata ayıklama `Trace` sağlayıcısına düzeyi ve üzeri Günlükler gönderir. `Debug` Düzeyi ve üzeri Günlükler konsol sağlayıcısına gönderilir.

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

En düşük düzeyi açıkça ayarlamazsanız, varsayılan değer olur `Information`ve `Trace` `Debug` bu, günlüklerin yok sayılacağı anlamına gelir.

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
| Microsoft.AspNetCore.Mvc            | MVC ve Razor tanılama. Model bağlama, filtre yürütme, derlemeyi görüntüleme, eylem seçimi. |
| Microsoft.AspNetCore.Routing        | Eşleşen bilgileri yönlendirin. |
| Microsoft. AspNetCore. Server         | Bağlantı başlatın, durdurun ve canlı yanıtları koruyun. HTTPS sertifika bilgileri. |
| Microsoft.AspNetCore.StaticFiles    | Sunulan dosyalar. |
| Microsoft. EntityFrameworkCore       | Genel Entity Framework Core tanılama. Veritabanı etkinliği ve yapılandırması, değişiklik algılama, geçişler. |

## <a name="log-scopes"></a>Günlük kapsamları

 *Kapsam* bir mantıksal işlemler kümesini gruplandırabilir. Bu gruplandırma, kümenin bir parçası olarak oluşturulan her günlüğe aynı verileri eklemek için kullanılabilir. Örneğin, bir işlemin işlenmesi kapsamında oluşturulan her günlük işlem KIMLIĞI içerebilir.

Kapsam, <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> yöntemi tarafından `IDisposable` döndürülen ve atılana kadar sürer olan bir türdür. Bir `using` blok içinde günlükçü çağrılarını sarmalayarak kapsam kullanın:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Aşağıdaki kod konsol sağlayıcısı için kapsamları etkinleştirilir:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Kapsam tabanlı `IncludeScopes` günlüğe kaydetmeyi etkinleştirmek için konsol günlükçü seçeneğinin yapılandırılması gerekir.
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
* [H](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Stdout ve ASP.NET Core modüllü hata ayıklama günlüğü hakkında bilgi için bkz <xref:test/troubleshoot-azure-iis> . ve <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

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

[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) sağlayıcı paketi, [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) sınıfını (`Debug.WriteLine` Yöntem çağrıları) kullanarak günlük çıktısını yazar.

Linux 'ta, bu sağlayıcı günlükleri */var/log/Message*dosyasına yazar.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Olay kaynak sağlayıcısı

[Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) sağlayıcı paketi, adıyla `Microsoft-Extensions-Logging`bir olay kaynağı çapraz platformuna yazar. Windows 'da, sağlayıcı [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)kullanır.

```csharp
logging.AddEventSourceLogger();
```

Olay kaynak sağlayıcısı, konağı oluşturmak için çağrıldığında `CreateDefaultBuilder` otomatik olarak eklenir.

Günlükleri toplamak ve görüntülemek için [PerfView yardımcı programını](https://github.com/Microsoft/perfview) kullanın. ETW günlüklerini görüntülemeye yönelik başka araçlar da mevcuttur, ancak PerfView, ASP.NET Core tarafından yayınlanan ETW olaylarıyla çalışmak için en iyi deneyimi sağlar.

Bu sağlayıcı tarafından günlüğe kaydedilen olayları toplamak için PerfView 'ı yapılandırmak için, dizeyi `*Microsoft-Extensions-Logging` **ek sağlayıcılar** listesine ekleyin. (Dizenin başlangıcında yıldız işaretini kaçırmayın.)

![PerfView ek sağlayıcıları](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows olay günlüğü sağlayıcısı

[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sağlayıcı paketi, Windows olay günlüğüne günlük çıktısı gönderir.

```csharp
logging.AddEventLog();
```

[AddEventLog aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) , geçiş yapmanızı <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>sağlar. `null` Veya belirtilmemişse, aşağıdaki varsayılan ayarlar kullanılır:

* `LogName`&ndash; "Uygulama"
* `SourceName`&ndash; ".NET çalışma zamanı"
* `MachineName`&ndash; yerel makine

Olaylar, [Uyarı düzeyi ve üzeri](#log-level)için günlüğe kaydedilir. Olayları daha düşük günlüğe kaydetmek `Warning`için, günlük düzeyini açık olarak ayarlayın. Örneğin, *appSettings. JSON* dosyasına aşağıdakini ekleyin:

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>TraceSource sağlayıcısı

[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) sağlayıcı paketi <xref:System.Diagnostics.TraceSource> kitaplıklarını ve sağlayıcıları kullanır.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Addtracesource aşırı yüklemeleri](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) , bir kaynak anahtarı ve bir izleme dinleyicisi geçirmenize olanak sağlar.

Bu sağlayıcıyı kullanmak için, bir uygulamanın .NET Framework çalışması gerekir (.NET Core yerine). Sağlayıcı, iletileri örnek uygulamada <xref:System.Diagnostics.TextWriterTraceListener> kullanılan gibi çeşitli [dinleyicilerine](/dotnet/framework/debug-trace-profile/trace-listeners)yönlendirebilir.

### <a name="azure-app-service-provider"></a>Azure App Service sağlayıcı

[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) sağlayıcı paketi, günlükleri bir Azure App Service uygulamasının dosya sistemindeki metin dosyalarına ve bir Azure depolama hesabındaki [BLOB depolama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) alanına yazar.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Sağlayıcı paketi [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahil değildir. .NET Framework veya `Microsoft.AspNetCore.App` metapackage 'e başvuru yaparken, sağlayıcı paketini projeye ekleyin. 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Aşırı yükleme, geçiş yapmanızı sağlar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. Ayarlar nesnesi, günlük çıkış şablonu, blob adı ve dosya boyutu sınırı gibi varsayılan ayarları geçersiz kılabilir. (*Çıktı şablonu* , bir `ILogger` yöntem çağrısıyla birlikte sağlandığının yanı sıra tüm günlüklere uygulanan bir ileti şablonudur.)

App Service uygulamasına dağıtırken, uygulama, Azure portal **App Service** sayfasının [App Service Günlükler](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) bölümündeki ayarları kabul eder. Aşağıdaki ayarlar güncelleştirilirken, değişiklikler uygulamanın yeniden başlatılmasını veya yeniden dağıtımı gerekmeden hemen etkili olur.

* **Uygulama günlüğü (dosya sistemi)**
* **Uygulama günlüğü (blob)**

Günlük dosyaları için varsayılan konum *D:\\Home\\LogFiles\\uygulama* klasöründedir ve varsayılan dosya adı *Diagnostics-YYYYMMDD. txt*' dir. Varsayılan dosya boyutu sınırı 10 MB 'tır ve tutulan varsayılan en fazla dosya sayısı 2 ' dir. Varsayılan blob adı *{app-name} {timestamp}/yyyy/mm/dd/ss/{Guid}-ApplicationLog.txt*.

Sağlayıcı yalnızca proje Azure ortamında çalıştırıldığında çalışır. Proje yerel olarak&mdash;çalıştırıldığında, yerel dosyalara veya blob 'lar için yerel geliştirme depolamasına yazmazsa, bu, hiçbir etkiye sahip değildir.

#### <a name="azure-log-streaming"></a>Azure günlük akışı

Azure günlük akışı, günlük etkinliklerini gerçek zamanlı olarak görüntülemenize izin verir:

* Uygulama sunucusu
* Web sunucusu
* Başarısız istek izleme

Azure günlük akışını yapılandırmak için:

* Uygulamanızın Portal sayfasından **App Service günlükleri** sayfasına gidin.
* **Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.
* Günlük **düzeyini**seçin. Bu ayar, uygulamadaki diğer günlük sağlayıcılarını değil, yalnızca Azure günlük akışı için geçerlidir.

Uygulama iletilerini görüntülemek için **günlük akışı** sayfasına gidin. Uygulama tarafından `ILogger` arabirim aracılığıyla günlüğe kaydedilir.

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights izleme günlüğü

[Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) sağlayıcı paketi günlükleri Azure Application Insights yazar. Application Insights, bir Web uygulamasını izleyen ve telemetri verilerini sorgulamak ve analiz etmek için araçlar sağlayan bir hizmettir. Bu sağlayıcıyı kullanıyorsanız, Application Insights araçlarını kullanarak günlüklerinizi sorgulayabilir ve analiz edebilirsiniz.

Günlüğe kaydetme sağlayıcısı, ASP.NET Core için tüm kullanılabilir telemetri sağlayan paket olan [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'un bağımlılığı olarak eklenmiştir. Bu paketi kullanırsanız, sağlayıcı paketini yüklemek zorunda kalmazsınız.

ASP.NET 4. x için [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) paketini&mdash;kullanmayın.

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
1. Günlüğe kaydetme `ILoggerFactory` çerçevesi tarafından sağlanmış bir genişletme yöntemi çağırın.

Daha fazla bilgi için bkz. her sağlayıcının belgeleri. Üçüncü taraf günlüğü sağlayıcıları Microsoft tarafından desteklenmez.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
