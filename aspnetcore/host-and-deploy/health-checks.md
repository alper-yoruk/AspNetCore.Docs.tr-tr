---
title: ASP.NET Core'da sağlık kontrolleri
author: rick-anderson
description: Uygulamalar ve veritabanları gibi ASP.NET Core altyapısı için sistem durumu denetimlerini nasıl ayarlayacağınız hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 314e55c818cddf1dad2e3ec74d4d1e041ce7366f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664887"
---
# <a name="health-checks-in-aspnet-core"></a>ASP.NET Core'da sağlık kontrolleri

Yazar: [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu bildirmek için Sağlık Denetimleri Middleware ve kütüphaneler sunar.

Sağlık kontrolleri HTTP uç noktaları olarak bir uygulama tarafından ortaya çıkarır. Sistem durumu denetimi uç noktaları çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:

* Sistem durumu sondaları, bir uygulamanın durumunu kontrol etmek için konteyner orkestratörleri ve yük dengeleyicileri tarafından kullanılabilir. Örneğin, bir kapsayıcı orkestratör, yuvarlanan dağıtımı durdurarak veya bir kapsayıcıyı yeniden başlatarak başarısız bir sistem durumu denetimine yanıt verebilir. Yük dengeleyicisi, trafiği başarısız örnekten sağlıklı bir örneğe yönlendirme yaparak sağlıksız bir uygulamaya tepki verebilir.
* Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı durum için izlenebilir.
* Sistem durumu denetimleri, kullanılabilirliği ve normal işleyişi onaylamak için bir uygulamanın veritabanları ve harici hizmet bitiş noktaları gibi bağımlılıklarını sınayabilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama, bu konuda açıklanan senaryoörnekleri içerir. Belirli bir senaryo için örnek uygulamayı çalıştırmak için, proje klasöründeki [dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutunu komut kabuğunda kullanın. Örnek uygulamanın nasıl kullanılacağıyla ilgili ayrıntılar için örnek uygulamanın *README.md* dosyasına ve bu konudaki senaryo açıklamalarına bakın.

## <a name="prerequisites"></a>Ön koşullar

Sistem durumu denetimleri genellikle bir uygulamanın durumunu kontrol etmek için harici bir izleme hizmeti veya konteyner orchestrator ile kullanılır. Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağına karar verin. İzleme sistemi, hangi sistem durumu denetimlerinin oluşturulmasını ve uç noktalarının nasıl yapılandırılabildiğini belirler.

[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketi ASP.NET Core uygulamaları için dolaylı olarak başvurulmuyorum. Entity Framework Core kullanarak sistem durumu denetimleri gerçekleştirmek için [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) paketine bir paket başvurusu ekleyin.

Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek için başlangıç kodu sağlar. [Veritabanı sonda](#database-probe) senaryosu [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının durumunu denetler. [DbContext sonda](#entity-framework-core-dbcontext-probe) senaryosu bir EF `DbContext`Core kullanarak bir veritabanı denetler. Veritabanı senaryolarını incelemek için örnek uygulama:

* Bir veritabanı oluşturur ve *appsettings.json* dosyasında bağlantı dizesini sağlar.
* Proje dosyasında aşağıdaki paket başvuruları vardır:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

Başka bir sistem durumu denetimi senaryosu, sistem durumu denetimlerinin yönetim bağlantı noktasına nasıl filtre uygulandığını gösterir. Örnek uygulama, yönetim URL'sini ve yönetim bağlantı noktasını içeren bir *Properties/launchSettings.json* dosyası oluşturmanızı gerektirir. Daha fazla bilgi için bağlantı noktası bölümüne [göre Filtre'ye](#filter-by-port) bakın.

## <a name="basic-health-probe"></a>Temel sağlık sondası

Birçok uygulama için, uygulamanın istekleri *(canlılık)* işlemek için kullanılabilirliğini bildiren temel bir sağlık sondası yapılandırması, uygulamanın durumunu keşfetmek için yeterlidir.

Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu yanıtıyla bir URL bitiş noktasında yanıt vermesi için Sistem Durumu Denetimleri Aracı'nı çağırır. Varsayılan olarak, belirli bir bağımlılık veya alt sistemi sınamak için belirli bir sistem denetimi kaydedilmemiştir. Uygulama, sistem durumu bitiş noktası URL'sinde yanıt verebiliyorsa sağlıklı kabul edilir. Varsayılan yanıt yazarı durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ) istemciye düz metin yanıtı olarak yazar ve bu yanıtı [bir HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus.Nothealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durumunu gösterir.

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. 'yi arayarak `MapHealthChecks` bir sistem `Startup.Configure`durumu denetimi bitiş noktası oluşturun.

Örnek uygulamada, sağlık kontrolü bitiş noktası `/health` *(BasicStartup.cs)* olarak oluşturulur:

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Docker örneği

[Docker,](xref:host-and-deploy/docker/index) temel sistem `HEALTHCHECK` durumu denetimi yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilecek yerleşik bir yönerge sunar:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Sistem durumu denetimleri oluşturma

Sistem durumu denetimleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> arabirimi uygulanarak oluşturulur. Yöntem, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> durumu `Healthy`, `Degraded`, veya `Unhealthy`. Sonuç, yapılandırılabilir durum koduyla düz metin yanıtı olarak yazılır (yapılandırma [Sistem Durumu denetim seçenekleri](#health-check-options) bölümünde açıklanır). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>isteğe bağlı anahtar değeri çiftleri de döndürebilir.

Aşağıdaki `ExampleHealthCheck` sınıf, sistem durumu denetiminin düzenini gösterir. Sistem durumu denetimi mantığı `CheckHealthAsync` yönteme yerleştirilir. Aşağıdaki örnek, bir kukla `healthCheckResultHealthy`değişken, `true`için . Değeri `healthCheckResultHealthy` , `false` [HealthCheckResult.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum olarak ayarlanırsa döndürülür.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a>Sistem durumu kontrol hizmetlerini kaydedin

Türü `ExampleHealthCheck` ile <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> sağlık kontrol hizmetlerine `Startup.ConfigureServices`eklenir:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Aşağıdaki <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> örnekte gösterilen aşırı yük, sistem<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>durumu denetimi bir hata bildirdiğinde bildirmek için hata durumunu ( ) ayarlar. Hata durumu `null` (varsayılan) olarak ayarlanmışsa, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir. Bu aşırı yükleme, sistem durumu denetimi uygulaması ayarı onurlandırırsa, sistem durumu denetimi hatası oluştuğunda kitaplık tarafından gösterilen hata durumunun uygulama tarafından zorlandığı kitaplık yazarları için yararlı bir senaryodur.

*Etiketler,* sistem durumu denetimlerini filtrelemek için kullanılabilir [(Filtre sistem durumu denetimleri](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lambda işlevini de yerine getirebilir. Aşağıdaki örnekte, sistem durumu denetimi `Example` adı olarak belirtilir ve denetim her zaman sağlıklı bir durum döndürür:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Bağımsız <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> değişkenleri sistem durumu denetimi uygulamasına geçirmek için arayın. Aşağıdaki örnekte, `TestHealthCheckWithArgs` çağrıldığında <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> bir tamsayı ve kullanılacak bir dize kabul eder:

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

`TestHealthCheckWithArgs`tamsayı ve `AddTypeActivatedCheck` dize ile arayarak kaydedilir uygulamaya geçti:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Sağlık Denetimleri Yönlendirme'yi kullanma

In, `Startup.Configure` `MapHealthChecks` bitiş noktası URL'si veya göreli yolu olan bitiş noktası oluşturucuyu arayın:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Ana bilgisayar gerektirir

Sistem `RequireHost` durumu denetimi bitiş noktası için bir veya daha fazla izin verilen ana bilgisayar belirtmek için arayın. Ana bilgisayarlar punycode yerine Unicode olmalıdır ve bir bağlantı noktası içerebilir. Bir koleksiyon sağlanmazsa, herhangi bir ana bilgisayar kabul edilir.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Daha fazla bilgi için bağlantı noktası bölümüne [göre Filtre'ye](#filter-by-port) bakın.

### <a name="require-authorization"></a>Yetkilendirme gerektirme

Sistem `RequireAuthorization` durumu denetimi isteği bitiş noktasında Yetkilendirme Middleware'i çalıştırmak için arayın. Aşırı `RequireAuthorization` yükleme, bir veya daha fazla yetkilendirme ilkesini kabul eder. Bir ilke sağlanmadıysa, varsayılan yetkilendirme ilkesi kullanılır.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Kaynaklar Arası İstekleri (CORS) etkinleştirme

Bir tarayıcıdan el ile sistem durumu denetimleri gerçekleştirmek yaygın bir kullanım senaryosu `RequireCors` olmasa da, CORS Middleware sistem durumu denetimleri uç noktalarını arayarak etkinleştirilebilir. Aşırı `RequireCors` yükleme, CORS ilke oluşturucu temsilcisini ( )`CorsPolicyBuilder`veya bir ilke adı kabul eder. Bir ilke sağlanmadıysa, varsayılan CORS ilkesi kullanılır. Daha fazla bilgi için bkz. <xref:security/cors>.

## <a name="health-check-options"></a>Sistem durumu denetimi seçenekleri

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirme kiçin bir fırsat sağlar:

* [Filtre sağlık denetimleri](#filter-health-checks)
* [HTTP durum kodunu özelleştirme](#customize-the-http-status-code)
* [Önbellek üstbilgilerini bastırma](#suppress-cache-headers)
* [Çıktıyı özelleştirme](#customize-output)

### <a name="filter-health-checks"></a>Filtre sağlık denetimleri

Varsayılan olarak, Sistem Durumu Denetimleri Middleware tüm kayıtlı sistem durumu denetimlerini çalıştırın. Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> seçene boolean döndüren bir işlev sağlayın. Aşağıdaki örnekte, `Bar` sistem durumu denetimi işlevin koşullu deyimindeki`bar_tag`etiketiyle filtrelenir `true` ve burada yalnızca sağlık denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşirse `foo_tag` veya: `baz_tag`

`Startup.ConfigureServices` içinde:

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

In `Startup.Configure`, `Predicate` filtreler 'Bar' sağlık kontrolü dışarı. Sadece Foo ve Baz infaz.:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a>HTTP durum kodunu özelleştirme

Sağlık <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> durumunun eşlemasını HTTP durum kodlarına göre özelleştirmek için kullanın. Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir. Gereksinimlerinizi karşılamak için durum kodu değerlerini değiştirin.

`Startup.Configure` içinde:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a>Önbellek üstbilgilerini bastırma

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem Durumu Denetimleri Middleware yanıt önbelleğe önlemek için bir sonda yanıtı http üstleri ekleyip ekledi denetimleri. Değer (varsayılan) `false` ise, yanıt önbelleğini önlemek için ara yazılım `Cache-Control`, ve `Expires` `Pragma` üstbilgi kümeler veya üstbilgi geçersiz kılar. Değer ise, `true`ara yazılım yanıtın önbellek üstbilgisini değiştirmez.

`Startup.Configure` içinde:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Çıktıyı özelleştirme

In, `Startup.Configure`yanıt yazmak için bir temsilci için [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) seçeneğini ayarlayın:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Varsayılan [temsilci, HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değeriyle en az düz metin yanıtı yazar. Aşağıdaki özel temsilciler özel bir JSON yanıtı çıktı.

Örnek uygulamadan ilk örnek nasıl kullanılacağını <xref:System.Text.Json?displayProperty=fullName>gösterir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

İkinci örnek [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)nasıl kullanılacağını gösterir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

Örnek `SYSTEM_TEXT_JSON` uygulamada, `Newtonsoft.Json` sürümünü etkinleştirmek için `WriteResponse` [CustomWriterStartup.cs'daki önişlemci yönergesini](xref:index#preprocessor-directives-in-sample-code) yorumla. *CustomWriterStartup.cs*

Sistem durumu denetimleri API, karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz, çünkü biçim izleme sistemi seçtiğinize özgüdür. Yanıtı gerektiğinde önceki örneklerde özelleştirin. JSON serileştirme hakkında daha `System.Text.Json`fazla bilgi için , [json'u .NET'te nasıl seri hale getirmek ve deserialize etmek için](/dotnet/standard/serialization/system-text-json-how-to)bkz.

## <a name="database-probe"></a>Veritabanı sondası

Sistem durumu denetimi, veritabanının normal yanıt verilip vermeyiş olmadığını belirtmek için boolean testi olarak çalışacak bir veritabanı sorgusu belirtebilir.

Örnek uygulama, bir SQL Server veritabanında sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamaları için bir sistem durumu denetimi kitaplığı olan [AspNetCore.Diagnostics.HealthChecks'i](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır. `AspNetCore.Diagnostics.HealthChecks`veritabanına `SELECT 1` bağlantının sağlıklı olduğunu doğrulamak için veritabanına karşı bir sorgu yürütür.

> [!WARNING]
> Bir sorguyla veritabanı bağlantısını denetlerken, hızla dönen bir sorgu seçin. Sorgu yaklaşımı veritabanıaşırı yükleme ve performansını düşürme riskini çalıştırıyor. Çoğu durumda, bir test sorgusu çalıştırma gerekli değildir. Yalnızca veritabanına başarılı bir bağlantı yapmak yeterlidir. Bir sorgu çalıştırmak için gerekli bulursanız, gibi `SELECT 1`basit bir SELECT sorgusu seçin.

[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)için bir paket referans ekleyin.

Örnek uygulamanın *appsettings.json* dosyasında geçerli bir veritabanı bağlantı dizesi verin. Uygulama, aşağıdakiler adlı `HealthCheckSample`bir SQL Server veritabanı kullanır:

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. Örnek uygulama veritabanının bağlantı dizesiyle `AddSqlServer` yöntemi çağırır *(DbHealthStartup.cs):*

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası arayarak oluşturulur:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Örnek uygulamayı kullanarak veritabanı sondasenaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="entity-framework-core-dbcontext-probe"></a>Varlık Çerçeve Çekirdek DbContext sondası

Denetim, `DbContext` uygulamanın BIR EF Core `DbContext`için yapılandırılan veritabanıyla iletişim kurabileceğini doğrular. Denetim `DbContext` aşağıdaki uygulamalarda desteklenir:

* [Varlık Çerçevesi (EF) Çekirdeğini](/ef/core/)Kullanın.
* [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)için bir paket başvurusu ekleyin.

`AddDbContextCheck<TContext>`için `DbContext`bir sağlık kontrolü kaydeder. Yöntem `DbContext` olarak `TContext` verilir. Hata durumunu, etiketleri ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.

Varsayılan olarak:

* EF `DbContextHealthCheck` Core'un `CanConnectAsync` yöntemini çağırır. Aşırı yükleme yöntemini kullanarak `AddDbContextCheck` sistem durumu denetlerken hangi işlemin çalıştırılabileceğini özelleştirebilirsiniz.
* Sistem durumu denetiminin adı `TContext` türün adıdır.

Örnek uygulamada, `AppDbContext` *(DbContextHealthStartup.cs)* bir hizmet olarak `Startup.ConfigureServices` sağlanır `AddDbContextCheck` ve bir hizmet olarak kaydedilir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası arayarak oluşturulur:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Örnek uygulamayı `DbContext` kullanarak sonda senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde bulunmadığını doğrulayın. Veritabanı varsa, silin.

Proje klasöründen komut kabuğunda aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario dbcontext
```

Uygulama çalışmaya devam ettikten sonra, tarayıcıda `/health` bitiş noktasına istekte bulunarak sistem durumunu kontrol edin. Veritabanı ve `AppDbContext` yok, bu nedenle uygulama aşağıdaki yanıtı sağlar:

```
Unhealthy
```

Veritabanını oluşturmak için örnek uygulamayı tetikleyin. Bir istekte `/createdatabase`bulunun. Uygulama şu yanıtı verir:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

`/health` Bitiş noktasına bir istekte bulunun. Veritabanı ve bağlam vardır, bu nedenle uygulama yanıt verir:

```
Healthy
```

Veritabanını silmek için örnek uygulamayı tetikleyin. Bir istekte `/deletedatabase`bulunun. Uygulama şu yanıtı verir:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

`/health` Bitiş noktasına bir istekte bulunun. Uygulama sağlıksız bir yanıt sağlar:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Ayrı hazırlık ve canlılık sondaları

Bazı barındırma senaryolarında, iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:

* Uygulama çalışıyor ama henüz istek almaya hazır değil. Bu durum uygulamanın *hazır olmasıdır.*
* Uygulama çalışıyor ve isteklere yanıt veriyor. Bu durum uygulamanın *canlılığıdır.*

Hazırlık denetimi genellikle uygulamanın tüm alt sistemlerinin ve kaynaklarının kullanılabilir olup olmadığını belirlemek için daha kapsamlı ve zaman alan bir denetim ler kümesi gerçekleştirir. Canlılık denetimi, uygulamanın istekleri işlemek için kullanılabilir olup olmadığını belirlemek için yalnızca hızlı bir denetim gerçekleştirir. Uygulama hazırlık kontrolünden geçtikten sonra, pahalı hazırlık kontrolleri&mdash;seti ile uygulamayı daha fazla yükmeye gerek yoktur.

Örnek uygulama, [Barındırılan Hizmette](xref:fundamentals/host/hosted-services)uzun süren başlangıç görevinin tamamlanamasını bildirmek için bir sistem durumu denetimi içerir. Barındırılan `StartupHostedServiceHealthCheck` hizmetin `StartupTaskCompleted`uzun süren görevi tamamlandığında `true` ayarlanabileceği bir özelliği ortaya çıkarır *(StartupHostedServiceHealthCheck.cs):*

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Uzun süren arka plan görevi [Barındırılan Hizmet](xref:fundamentals/host/hosted-services) *(Hizmetler/BaşlangıçHostedService)* tarafından başlatılır. Görevin sonunda, `StartupHostedServiceHealthCheck.StartupTaskCompleted` `true`ayarlanır:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Sağlık kontrolü, barındırılan hizmetle <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` birlikte kaydedilir. Barındırılan hizmet in sağlık kontrolünde özelliği ayarlamak gerekir, çünkü sağlık kontrolü de hizmet konteyner *(LivenessProbeStartup.cs)* kaydedilir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası' nı arayarak oluşturulur. Örnek uygulamada, sistem durumu denetimi uç noktaları aşağıdaki noktalarda oluşturulur:

* `/health/ready`hazırlık kontrolü için. Hazır kontrol, sağlık denetimlerini etiketle birlikte `ready` sistem durumu denetimine filtreler.
* `/health/live`canlılık kontrolü için. Canlılık `StartupHostedServiceHealthCheck` `false` [denetimi, HealthCheckOptions.Predicate'de](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) dönerek filtreler (daha fazla bilgi için [bkz.](#filter-health-checks)

Aşağıdaki örnek kodda:

* Hazırlık denetimi, 'hazır' etiketiyle tüm kayıtlı çekleri kullanır.
* Tüm `Predicate` çekleri hariç tutar ve 200-Ok döndürün.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

Örnek uygulamayı kullanarak hazırlık/canlılık yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:

```dotnetcli
dotnet run --scenario liveness
```

Bir tarayıcıda, `/health/ready` 15 saniye geçene kadar birkaç kez ziyaret edin. Sağlık kontrolü ilk 15 saniye *sağlıksız* bildirir. 15 saniye sonra, bitiş noktası barındırılan hizmet tarafından uzun süren görevin tamamlanmasını yansıtan *Sağlıklı*raporlar.

Bu örnek, iki saniyelik<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> bir gecikmeyle ilk hazırlık denetimini çalıştıran bir Sağlık Denetimi Yayımcısı (uygulama) da oluşturur. Daha fazla bilgi için [Sağlık Denetimi Yayımcısı](#health-check-publisher) bölümüne bakın.

### <a name="kubernetes-example"></a>Kubernetes örneği

Ayrı hazırlık ve canlılık denetimleri kullanarak [Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır. Kubernetes'te, bir uygulamanın, temel veritabanı kullanılabilirliğinin sınanması gibi istekleri kabul etmeden önce zaman alan başlangıç çalışması gerçekleştirmesi gerekebilir. Ayrı denetimler kullanmak, orkestratörün uygulamanın çalışıp çalışmadığını ancak henüz hazır olup olmadığını veya uygulamanın başlatılıp başlatılamadığını ayırt etmesini sağlar. Kubernetes'teki hazırlık ve canlılık sondaları hakkında daha fazla bilgi için Kubernetes belgelerinde [Canlılık ve Hazırlık Sondalarını Yapılandırıyorum'a](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) bakın.

Aşağıdaki örnek, Bir Kubernetes hazırlık sondası yapılandırmasını gösterir:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Özel yanıt yazarı ile metrik tabanlı sonda

Örnek uygulama, özel bir yanıt yazarıyla bir bellek durumu denetimi gösterir.

`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) daha fazlasını kullanıyorsa, bozulmuş bir durum bildirir. Uygulama <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> için Çöp Toplayıcı (GC) bilgileri *(MemoryHealthCheck.cs)* içerir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. Sağlık kontrolünü sağlayarak etkinleştirmek <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>yerine, `MemoryHealthCheck` hizmet olarak kaydedilir. Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler sağlık kontrol hizmetleri ve ara yazılımlar için kullanılabilir. Sağlık kontrolü hizmetlerini Singleton hizmetleri olarak kaydetmenizi öneririz.

Örnek uygulamanın *CustomWriterStartup.cs:*

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Bir sistem durumu denetimi bitiş `MapHealthChecks` `Startup.Configure`noktası' nı arayarak oluşturulur. Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter özelliğini, sistem durumu denetimi yürütürken özel bir JSON yanıtı çıktısı için> <bir `WriteResponse` temsilci sağlanır:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Temsilci `WriteResponse` bir JSON nesnesi `CompositeHealthCheckResult` içine biçimlendirin ve sistem durumu denetimi yanıtı için JSON çıktısı verir. Daha fazla bilgi için [çıktıyı Özelleştir](#customize-output) bölümüne bakın.

Örnek uygulamayı kullanarak özel yanıt yazar çıktısı ile metrik tabanlı sondayı çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks,](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) disk depolama ve maksimum değer canlılık denetimleri de dahil olmak üzere metrik tabanlı sistem durumu denetimi senaryoları içerir.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="filter-by-port"></a>Bağlantı noktasına göre filtrele

Sistem `RequireHost` `MapHealthChecks` durumu denetimi isteklerini belirtilen bağlantı noktasıyla sınırlamak için bir bağlantı noktası belirten bir URL deseniyle arayın. Bu genellikle hizmetleri izlemek için bir bağlantı noktası ortaya çıkarmak için bir kapsayıcı ortamında kullanılır.

Örnek uygulama, Çevre Değişken [yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables-configuration-provider)kullanarak bağlantı noktasını yapılandırır. Bağlantı noktası *launchSettings.json* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına aktarılır. Ayrıca, sunucuyu yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.

Yönetim bağlantı noktası yapılandırmasını göstermek için örnek uygulamayı kullanmak için, *özellikler* klasöründe *launchSettings.json* dosyasını oluşturun.

Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings.json* dosyası örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulmalıdır:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. 'yi arayarak `MapHealthChecks` bir sistem `Startup.Configure`durumu denetimi bitiş noktası oluşturun.

Örnek uygulamada, bitiş noktasında `RequireHost` ki bir `Startup.Configure` çağrı yönetim bağlantı noktasını yapılandırmadan belirtir:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Son noktalar örnek uygulamada `Startup.Configure`'da oluşturulur. Aşağıdaki örnek kodda:

* Hazırlık denetimi, 'hazır' etiketiyle tüm kayıtlı çekleri kullanır.
* Tüm `Predicate` çekleri hariç tutar ve 200-Ok döndürün.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> Yönetim bağlantı noktasını açıkça kodda ayarlayarak örnek uygulamada *launchSettings.json* dosyasını oluşturmaktan kaçınabilirsiniz. Oluşturulan *Program.cs,* <xref:Microsoft.Extensions.Hosting.HostBuilder> bir çağrı ekleyin <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> ve uygulamanın yönetim bağlantı noktası bitiş noktasını sağlayın. `Configure` *ManagementPortStartup.cs*olarak, aşağıdakilerle yönetim bağlantı noktasını belirtin: `RequireHost`
>
> *Program.cs*:
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Örnek uygulamayı kullanarak yönetim bağlantı noktası yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Sistem durumu denetimi kitaplığı dağıtma

Sistem durumu denetimini kitaplık olarak dağıtmak için:

1. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> Arabirimi bağımsız bir sınıf olarak uygulayan bir sistem durumu denetimi yazın. Sınıf [bağımlılık enjeksiyonu (DI),](xref:fundamentals/dependency-injection)tür etkinleştirme ve yapılandırma verilerine erişmek için [adlandırılmış seçeneklere](xref:fundamentals/configuration/options) güvenebilir.

   Sağlık kontrolleri mantığı: `CheckHealthAsync`

   * `data1`ve `data2` sondanın sistem durumu kontrol mantığını çalıştırmak için yöntemde kullanılır.
   * `AccessViolationException`işlenir.

   Bir <xref:System.AccessViolationException> durum oluştuğunda, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> kullanıcıların <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> sistem durumu denetimleri hata durumunu yapılandırmasına izin vermek için döndürülür.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. Tüketen uygulamanın yönteminde aradığı parametreleri `Startup.Configure` içeren bir uzantı yöntemi yazın. Aşağıdaki örnekte, aşağıdaki sistem durumu denetimi yöntemi imzasını varsayalım:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Önceki imza, sistem `ExampleHealthCheck` durumu denetimi sondası mantığını işlemek için ek veri gerektirdiğini gösterir. Veriler, sistem durumu denetimi bir uzantı yöntemiyle kaydedildiğinde sistem durumu denetimi örneğini oluşturmak için kullanılan temsilciye sağlanır. Aşağıdaki örnekte, arayan isteğe bağlı olarak belirtir:

   * sağlık kontrol`name`adı ( ). Eğer `null` `example_health_check` , kullanılır.
   * sistem durumu denetimi için`data1`dize veri noktası ( ).
   * sistem durumu denetimi için sonda`data2`veri noktası ( ). Eğer `null` `1` , kullanılır.
   * hata durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ). Varsayılan değer: `null`. If `null`, [HealthStatus.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bir başarısızlık durumu için bildirilir.
   * etiketleri`IEnumerable<string>`( ).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Sağlık Kontrolü Yayıncı

Servis <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> konteynerine bir şey eklendiğinde, sağlık kontrol sistemi sağlık `PublishAsync` kontrollerinizi düzenli aralıklarla yürütür ve sonuçla birlikte çağrıları yerine getirmez. Bu, her işlemin sistem durumunu belirlemek için izleme sistemini periyodik olarak aramasını bekleyen push tabanlı sistem senaryosunda yararlıdır.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Arabirimin tek bir yöntemi vardır:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>ayarlamanızı sağlar:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Örnekleri yürütmeden önce uygulama başladıktan sonra uygulanan ilk <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> gecikme. Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemeler için geçerli değildir. Varsayılan değer beş saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> süresi. Varsayılan değer 30 saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> Varsa `null` (varsayılan), sistem durumu denetimi yayımcısı hizmeti tüm kayıtlı sistem durumu denetimlerini çalıştırAr. Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, denetim kümesini filtreleyen bir işlev sağlayın. Yüklem her dönem değerlendirilir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnekler için sistem durumu denetimlerini yürütmek için zaman arası. Bir <xref:System.Threading.Timeout.InfiniteTimeSpan> zaman arası olmadan yürütmek için kullanın. Varsayılan değer 30 saniyedir.

Örnek uygulamada, `ReadinessPublisher` bir <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulamadır. Sistem durumu denetimi durumu, her denetim için günlük düzeyinde günlüğe kaydedilir:

* Bilgi<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) sağlık kontrolleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>durumu ise .
* Hata<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) durum <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ya <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>da .

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazırlık denetiminde iki saniyelik bir başlatma gecikmesi vardır ve denetimi her 30 saniyede bir çalıştırın. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Uygulamayı etkinleştirmek için, örnek `ReadinessPublisher` bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection) konteynerinde tek tonlu bir hizmet olarak kaydedilir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) [Uygulama Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayıncılar içerir.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="restrict-health-checks-with-mapwhen"></a>MapWhen ile sistem durumu denetimlerini kısıtlama

Sistem <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> durumu denetimi uç noktaları için istek ardışık dallandırmak için kullanın.

Aşağıdaki örnekte, `MapWhen` `api/HealthCheck` bitiş noktası için bir GET isteği alınırsa, Sağlık Denetimleri Ara ware etkinleştirmek için istek ardışık dalları:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu bildirmek için Sağlık Denetimleri Middleware ve kütüphaneler sunar.

Sağlık kontrolleri HTTP uç noktaları olarak bir uygulama tarafından ortaya çıkarır. Sistem durumu denetimi uç noktaları çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:

* Sistem durumu sondaları, bir uygulamanın durumunu kontrol etmek için konteyner orkestratörleri ve yük dengeleyicileri tarafından kullanılabilir. Örneğin, bir kapsayıcı orkestratör, yuvarlanan dağıtımı durdurarak veya bir kapsayıcıyı yeniden başlatarak başarısız bir sistem durumu denetimine yanıt verebilir. Yük dengeleyicisi, trafiği başarısız örnekten sağlıklı bir örneğe yönlendirme yaparak sağlıksız bir uygulamaya tepki verebilir.
* Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı durum için izlenebilir.
* Sistem durumu denetimleri, kullanılabilirliği ve normal işleyişi onaylamak için bir uygulamanın veritabanları ve harici hizmet bitiş noktaları gibi bağımlılıklarını sınayabilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama, bu konuda açıklanan senaryoörnekleri içerir. Belirli bir senaryo için örnek uygulamayı çalıştırmak için, proje klasöründeki [dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutunu komut kabuğunda kullanın. Örnek uygulamanın nasıl kullanılacağıyla ilgili ayrıntılar için örnek uygulamanın *README.md* dosyasına ve bu konudaki senaryo açıklamalarına bakın.

## <a name="prerequisites"></a>Ön koşullar

Sistem durumu denetimleri genellikle bir uygulamanın durumunu kontrol etmek için harici bir izleme hizmeti veya konteyner orchestrator ile kullanılır. Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağına karar verin. İzleme sistemi, hangi sistem durumu denetimlerinin oluşturulmasını ve uç noktalarının nasıl yapılandırılabildiğini belirler.

Başvuru [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) veya [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketine bir paket başvuru ekleyin.

Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek için başlangıç kodu sağlar. [Veritabanı sonda](#database-probe) senaryosu [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının durumunu denetler. [DbContext sonda](#entity-framework-core-dbcontext-probe) senaryosu bir EF `DbContext`Core kullanarak bir veritabanı denetler. Veritabanı senaryolarını incelemek için örnek uygulama:

* Bir veritabanı oluşturur ve *appsettings.json* dosyasında bağlantı dizesini sağlar.
* Proje dosyasında aşağıdaki paket başvuruları vardır:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

Başka bir sistem durumu denetimi senaryosu, sistem durumu denetimlerinin yönetim bağlantı noktasına nasıl filtre uygulandığını gösterir. Örnek uygulama, yönetim URL'sini ve yönetim bağlantı noktasını içeren bir *Properties/launchSettings.json* dosyası oluşturmanızı gerektirir. Daha fazla bilgi için bağlantı noktası bölümüne [göre Filtre'ye](#filter-by-port) bakın.

## <a name="basic-health-probe"></a>Temel sağlık sondası

Birçok uygulama için, uygulamanın istekleri *(canlılık)* işlemek için kullanılabilirliğini bildiren temel bir sağlık sondası yapılandırması, uygulamanın durumunu keşfetmek için yeterlidir.

Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu yanıtıyla bir URL bitiş noktasında yanıt vermesi için Sistem Durumu Denetimleri Aracı'nı çağırır. Varsayılan olarak, belirli bir bağımlılık veya alt sistemi sınamak için belirli bir sistem denetimi kaydedilmemiştir. Uygulama, sistem durumu bitiş noktası URL'sinde yanıt verebiliyorsa sağlıklı kabul edilir. Varsayılan yanıt yazarı durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ) istemciye düz metin yanıtı olarak yazar ve bu yanıtı [bir HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus.Nothealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durumunu gösterir.

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. İstek işleme ardışık boru hattı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> ile Sağlık Denetimleri Middleware için bir bitiş noktası `Startup.Configure`ekleyin.

Örnek uygulamada, sağlık kontrolü bitiş noktası `/health` *(BasicStartup.cs)* olarak oluşturulur:

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Docker örneği

[Docker,](xref:host-and-deploy/docker/index) temel sistem `HEALTHCHECK` durumu denetimi yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilecek yerleşik bir yönerge sunar:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Sistem durumu denetimleri oluşturma

Sistem durumu denetimleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> arabirimi uygulanarak oluşturulur. Yöntem, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> durumu `Healthy`, `Degraded`, veya `Unhealthy`. Sonuç, yapılandırılabilir durum koduyla düz metin yanıtı olarak yazılır (yapılandırma [Sistem Durumu denetim seçenekleri](#health-check-options) bölümünde açıklanır). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>isteğe bağlı anahtar değeri çiftleri de döndürebilir.

### <a name="example-health-check"></a>Örnek sistem durumu denetimi

Aşağıdaki `ExampleHealthCheck` sınıf, sistem durumu denetiminin düzenini gösterir. Sistem durumu denetimi mantığı `CheckHealthAsync` yönteme yerleştirilir. Aşağıdaki örnek, bir kukla `healthCheckResultHealthy`değişken, `true`için . Değeri `healthCheckResultHealthy` , `false` [HealthCheckResult.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum olarak ayarlanırsa döndürülür.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>Sistem durumu kontrol hizmetlerini kaydedin

Tür, `ExampleHealthCheck` sağlık denetimi hizmetlerine `Startup.ConfigureServices` aşağıdakilerle <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>eklenir:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Aşağıdaki <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> örnekte gösterilen aşırı yük, sistem<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>durumu denetimi bir hata bildirdiğinde bildirmek için hata durumunu ( ) ayarlar. Hata durumu `null` (varsayılan) olarak ayarlanmışsa, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir. Bu aşırı yükleme, sistem durumu denetimi uygulaması ayarı onurlandırırsa, sistem durumu denetimi hatası oluştuğunda kitaplık tarafından gösterilen hata durumunun uygulama tarafından zorlandığı kitaplık yazarları için yararlı bir senaryodur.

*Etiketler,* sistem durumu denetimlerini filtrelemek için kullanılabilir [(Filtre sistem durumu denetimleri](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lambda işlevini de yerine getirebilir. Aşağıdaki `Startup.ConfigureServices` örnekte, sistem durumu denetimi `Example` adı olarak belirtilir ve denetim her zaman sağlıklı bir durum döndürür:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Sağlık Kontrollerini Ara Yazılım Kullanma

Son `Startup.Configure`nokta <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> URL'si veya göreli yolu olan işleme ardışık hattını çağırın:

```csharp
app.UseHealthChecks("/health");
```

Sistem durumu denetimleri belirli bir bağlantı noktasında dinleyecekse, bağlantı noktasını ayarlamak <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> için aşırı yük kullanın (bağlantı noktası bölümüne göre [Filtre'de](#filter-by-port) daha ayrıntılı olarak açıklanmıştır):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Sistem durumu denetimi seçenekleri

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirme kiçin bir fırsat sağlar:

* [Filtre sağlık denetimleri](#filter-health-checks)
* [HTTP durum kodunu özelleştirme](#customize-the-http-status-code)
* [Önbellek üstbilgilerini bastırma](#suppress-cache-headers)
* [Çıktıyı özelleştirme](#customize-output)

### <a name="filter-health-checks"></a>Filtre sağlık denetimleri

Varsayılan olarak, Sistem Durumu Denetimleri Middleware tüm kayıtlı sistem durumu denetimlerini çalıştırın. Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> seçene boolean döndüren bir işlev sağlayın. Aşağıdaki örnekte, `Bar` sistem durumu denetimi işlevin koşullu deyimindeki`bar_tag`etiketiyle filtrelenir `true` ve burada yalnızca sağlık denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşirse `foo_tag` veya: `baz_tag`

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>HTTP durum kodunu özelleştirme

Sağlık <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> durumunun eşlemasını HTTP durum kodlarına göre özelleştirmek için kullanın. Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir. Gereksinimlerinizi karşılamak için durum kodu değerlerini değiştirin.

`Startup.Configure` içinde:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a>Önbellek üstbilgilerini bastırma

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem Durumu Denetimleri Middleware yanıt önbelleğe önlemek için bir sonda yanıtı http üstleri ekleyip ekledi denetimleri. Değer (varsayılan) `false` ise, yanıt önbelleğini önlemek için ara yazılım `Cache-Control`, ve `Expires` `Pragma` üstbilgi kümeler veya üstbilgi geçersiz kılar. Değer ise, `true`ara yazılım yanıtın önbellek üstbilgisini değiştirmez.

`Startup.Configure` içinde:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Çıktıyı özelleştirme

Seçenek, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> yanıtı yazmak için kullanılan bir temsilcialır veya ayarlar. Varsayılan [temsilci, HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değeriyle en az düz metin yanıtı yazar.

`Startup.Configure` içinde:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Varsayılan [temsilci, HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değeriyle en az düz metin yanıtı yazar. Aşağıdaki özel temsilci, `WriteResponse`özel bir JSON yanıtı çıktıları:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

Sistem, karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz, çünkü biçim izleme sistemi seçtiğinize özgüdür. İhtiyaçlarınızı karşılamak için `JObject` gerekli olduğu gibi yukarıdaki örnekte özelleştirmek için çekinmeyin.

## <a name="database-probe"></a>Veritabanı sondası

Sistem durumu denetimi, veritabanının normal yanıt verilip vermeyiş olmadığını belirtmek için boolean testi olarak çalışacak bir veritabanı sorgusu belirtebilir.

Örnek uygulama, bir SQL Server veritabanında sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamaları için bir sistem durumu denetimi kitaplığı olan [AspNetCore.Diagnostics.HealthChecks'i](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır. `AspNetCore.Diagnostics.HealthChecks`veritabanına `SELECT 1` bağlantının sağlıklı olduğunu doğrulamak için veritabanına karşı bir sorgu yürütür.

> [!WARNING]
> Bir sorguyla veritabanı bağlantısını denetlerken, hızla dönen bir sorgu seçin. Sorgu yaklaşımı veritabanıaşırı yükleme ve performansını düşürme riskini çalıştırıyor. Çoğu durumda, bir test sorgusu çalıştırma gerekli değildir. Yalnızca veritabanına başarılı bir bağlantı yapmak yeterlidir. Bir sorgu çalıştırmak için gerekli bulursanız, gibi `SELECT 1`basit bir SELECT sorgusu seçin.

[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)için bir paket referans ekleyin.

Örnek uygulamanın *appsettings.json* dosyasında geçerli bir veritabanı bağlantı dizesi verin. Uygulama, aşağıdakiler adlı `HealthCheckSample`bir SQL Server veritabanı kullanır:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. Örnek uygulama veritabanının bağlantı dizesiyle `AddSqlServer` yöntemi çağırır *(DbHealthStartup.cs):*

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Uygulama işleme boru hattında Sağlık Kontrolleri `Startup.Configure`Middleware çağrı:

```csharp
app.UseHealthChecks("/health");
```

Örnek uygulamayı kullanarak veritabanı sondasenaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="entity-framework-core-dbcontext-probe"></a>Varlık Çerçeve Çekirdek DbContext sondası

Denetim, `DbContext` uygulamanın BIR EF Core `DbContext`için yapılandırılan veritabanıyla iletişim kurabileceğini doğrular. Denetim `DbContext` aşağıdaki uygulamalarda desteklenir:

* [Varlık Çerçevesi (EF) Çekirdeğini](/ef/core/)Kullanın.
* [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)için bir paket başvurusu ekleyin.

`AddDbContextCheck<TContext>`için `DbContext`bir sağlık kontrolü kaydeder. Yöntem `DbContext` olarak `TContext` verilir. Hata durumunu, etiketleri ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.

Varsayılan olarak:

* EF `DbContextHealthCheck` Core'un `CanConnectAsync` yöntemini çağırır. Aşırı yükleme yöntemini kullanarak `AddDbContextCheck` sistem durumu denetlerken hangi işlemin çalıştırılabileceğini özelleştirebilirsiniz.
* Sistem durumu denetiminin adı `TContext` türün adıdır.

Örnek uygulamada, `AppDbContext` *(DbContextHealthStartup.cs)* bir hizmet olarak `Startup.ConfigureServices` sağlanır `AddDbContextCheck` ve bir hizmet olarak kaydedilir:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Örnek uygulamada, `UseHealthChecks` Sağlık Denetimleri Middleware `Startup.Configure`ekler.

```csharp
app.UseHealthChecks("/health");
```

Örnek uygulamayı `DbContext` kullanarak sonda senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde bulunmadığını doğrulayın. Veritabanı varsa, silin.

Proje klasöründen komut kabuğunda aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario dbcontext
```

Uygulama çalışmaya devam ettikten sonra, tarayıcıda `/health` bitiş noktasına istekte bulunarak sistem durumunu kontrol edin. Veritabanı ve `AppDbContext` yok, bu nedenle uygulama aşağıdaki yanıtı sağlar:

```
Unhealthy
```

Veritabanını oluşturmak için örnek uygulamayı tetikleyin. Bir istekte `/createdatabase`bulunun. Uygulama şu yanıtı verir:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

`/health` Bitiş noktasına bir istekte bulunun. Veritabanı ve bağlam vardır, bu nedenle uygulama yanıt verir:

```
Healthy
```

Veritabanını silmek için örnek uygulamayı tetikleyin. Bir istekte `/deletedatabase`bulunun. Uygulama şu yanıtı verir:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

`/health` Bitiş noktasına bir istekte bulunun. Uygulama sağlıksız bir yanıt sağlar:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Ayrı hazırlık ve canlılık sondaları

Bazı barındırma senaryolarında, iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:

* Uygulama çalışıyor ama henüz istek almaya hazır değil. Bu durum uygulamanın *hazır olmasıdır.*
* Uygulama çalışıyor ve isteklere yanıt veriyor. Bu durum uygulamanın *canlılığıdır.*

Hazırlık denetimi genellikle uygulamanın tüm alt sistemlerinin ve kaynaklarının kullanılabilir olup olmadığını belirlemek için daha kapsamlı ve zaman alan bir denetim ler kümesi gerçekleştirir. Canlılık denetimi, uygulamanın istekleri işlemek için kullanılabilir olup olmadığını belirlemek için yalnızca hızlı bir denetim gerçekleştirir. Uygulama hazırlık kontrolünden geçtikten sonra, pahalı hazırlık kontrolleri&mdash;seti ile uygulamayı daha fazla yükmeye gerek yoktur.

Örnek uygulama, [Barındırılan Hizmette](xref:fundamentals/host/hosted-services)uzun süren başlangıç görevinin tamamlanamasını bildirmek için bir sistem durumu denetimi içerir. Barındırılan `StartupHostedServiceHealthCheck` hizmetin `StartupTaskCompleted`uzun süren görevi tamamlandığında `true` ayarlanabileceği bir özelliği ortaya çıkarır *(StartupHostedServiceHealthCheck.cs):*

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Uzun süren arka plan görevi [Barındırılan Hizmet](xref:fundamentals/host/hosted-services) *(Hizmetler/BaşlangıçHostedService)* tarafından başlatılır. Görevin sonunda, `StartupHostedServiceHealthCheck.StartupTaskCompleted` `true`ayarlanır:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Sağlık kontrolü, barındırılan hizmetle <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` birlikte kaydedilir. Barındırılan hizmet in sağlık kontrolünde özelliği ayarlamak gerekir, çünkü sağlık kontrolü de hizmet konteyner *(LivenessProbeStartup.cs)* kaydedilir:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Uygulama işleme ardışık boru hattında `Startup.Configure`Sağlık Denetimleri Middleware'i arayın. Örnek uygulamada, hazır kontrol ve `/health/ready` `/health/live` canlılık denetimi için sağlık denetimi uç noktaları oluşturulur. Hazır kontrol, sağlık denetimlerini etiketle birlikte `ready` sistem durumu denetimine filtreler. Canlılık [denetimi, HealthCheckOptions.Predicate'de](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (daha fazla bilgi için [bkz. Filtre sağlık denetimlerine](#filter-health-checks)bakın) dönerek `StartupHostedServiceHealthCheck` `false` filtreler:

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

Örnek uygulamayı kullanarak hazırlık/canlılık yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:

```dotnetcli
dotnet run --scenario liveness
```

Bir tarayıcıda, `/health/ready` 15 saniye geçene kadar birkaç kez ziyaret edin. Sağlık kontrolü ilk 15 saniye *sağlıksız* bildirir. 15 saniye sonra, bitiş noktası barındırılan hizmet tarafından uzun süren görevin tamamlanmasını yansıtan *Sağlıklı*raporlar.

Bu örnek, iki saniyelik<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> bir gecikmeyle ilk hazırlık denetimini çalıştıran bir Sağlık Denetimi Yayımcısı (uygulama) da oluşturur. Daha fazla bilgi için [Sağlık Denetimi Yayımcısı](#health-check-publisher) bölümüne bakın.

### <a name="kubernetes-example"></a>Kubernetes örneği

Ayrı hazırlık ve canlılık denetimleri kullanarak [Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır. Kubernetes'te, bir uygulamanın, temel veritabanı kullanılabilirliğinin sınanması gibi istekleri kabul etmeden önce zaman alan başlangıç çalışması gerçekleştirmesi gerekebilir. Ayrı denetimler kullanmak, orkestratörün uygulamanın çalışıp çalışmadığını ancak henüz hazır olup olmadığını veya uygulamanın başlatılıp başlatılamadığını ayırt etmesini sağlar. Kubernetes'teki hazırlık ve canlılık sondaları hakkında daha fazla bilgi için Kubernetes belgelerinde [Canlılık ve Hazırlık Sondalarını Yapılandırıyorum'a](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) bakın.

Aşağıdaki örnek, Bir Kubernetes hazırlık sondası yapılandırmasını gösterir:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Özel yanıt yazarı ile metrik tabanlı sonda

Örnek uygulama, özel bir yanıt yazarıyla bir bellek durumu denetimi gösterir.

`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) daha fazlasını kullanıyorsa sağlıksız bir durum bildirir. Uygulama <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> için Çöp Toplayıcı (GC) bilgileri *(MemoryHealthCheck.cs)* içerir:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. Sağlık kontrolünü sağlayarak etkinleştirmek <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>yerine, `MemoryHealthCheck` hizmet olarak kaydedilir. Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler sağlık kontrol hizmetleri ve ara yazılımlar için kullanılabilir. Sağlık kontrolü hizmetlerini Singleton hizmetleri olarak kaydetmenizi öneririz.

Örnek uygulamada (*CustomWriterStartup.cs):*

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Uygulama işleme ardışık boru hattında `Startup.Configure`Sağlık Denetimleri Middleware'i arayın. Sistem `WriteResponse` durumu denetimi `ResponseWriter` yürütüldüğünde özel bir JSON yanıtı çıktısı için özel bir temsilci verilir:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

Yöntem, `WriteResponse` json `CompositeHealthCheckResult` nesnesine biçim verir ve sistem durumu denetimi yanıtı için JSON çıktısı verir:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Örnek uygulamayı kullanarak özel yanıt yazar çıktısı ile metrik tabanlı sondayı çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu uygulayın:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks,](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) disk depolama ve maksimum değer canlılık denetimleri de dahil olmak üzere metrik tabanlı sistem durumu denetimi senaryoları içerir.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="filter-by-port"></a>Bağlantı noktasına göre filtrele

Bağlantı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> noktasıyla arama yapmak, sistem durumu denetimi isteklerini belirtilen bağlantı noktasına kısıtlar. Bu genellikle hizmetleri izlemek için bir bağlantı noktası ortaya çıkarmak için bir kapsayıcı ortamında kullanılır.

Örnek uygulama, Çevre Değişken [yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables-configuration-provider)kullanarak bağlantı noktasını yapılandırır. Bağlantı noktası *launchSettings.json* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına aktarılır. Ayrıca, sunucuyu yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.

Yönetim bağlantı noktası yapılandırmasını göstermek için örnek uygulamayı kullanmak için, *özellikler* klasöründe *launchSettings.json* dosyasını oluşturun.

Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings.json* dosyası örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulmalıdır:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Sağlık kontrol hizmetlerini <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kaydedin. Yönetim bağlantı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> noktası *(ManagementPortStartup.cs)* belirtir arama:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> URL'leri ve yönetim bağlantı noktasını açıkça kodda ayarlayarak örnek uygulamada *launchSettings.json* dosyasını oluşturmaktan kaçınabilirsiniz. Oluşturulduğu *Program.cs,* <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> uygulamanın normal <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> yanıt bitiş noktası ve yönetim bağlantı noktası bitiş noktası için bir çağrı ekleyin ve sağlayın. Çağrıldığı <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> *ManagementPortStartup.cs,* yönetim bağlantı noktasını açıkça belirtin.
>
> *Program.cs*:
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Örnek uygulamayı kullanarak yönetim bağlantı noktası yapılandırma senaryosunu çalıştırmak için, proje klasöründen komut kabuğunda aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Sistem durumu denetimi kitaplığı dağıtma

Sistem durumu denetimini kitaplık olarak dağıtmak için:

1. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> Arabirimi bağımsız bir sınıf olarak uygulayan bir sistem durumu denetimi yazın. Sınıf [bağımlılık enjeksiyonu (DI),](xref:fundamentals/dependency-injection)tür etkinleştirme ve yapılandırma verilerine erişmek için [adlandırılmış seçeneklere](xref:fundamentals/configuration/options) güvenebilir.

   Sağlık kontrolleri mantığı: `CheckHealthAsync`

   * `data1`ve `data2` sondanın sistem durumu kontrol mantığını çalıştırmak için yöntemde kullanılır.
   * `AccessViolationException`işlenir.

   Bir <xref:System.AccessViolationException> durum oluştuğunda, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> kullanıcıların <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> sistem durumu denetimleri hata durumunu yapılandırmasına izin vermek için döndürülür.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. Tüketen uygulamanın yönteminde aradığı parametreleri `Startup.Configure` içeren bir uzantı yöntemi yazın. Aşağıdaki örnekte, aşağıdaki sistem durumu denetimi yöntemi imzasını varsayalım:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Önceki imza, sistem `ExampleHealthCheck` durumu denetimi sondası mantığını işlemek için ek veri gerektirdiğini gösterir. Veriler, sistem durumu denetimi bir uzantı yöntemiyle kaydedildiğinde sistem durumu denetimi örneğini oluşturmak için kullanılan temsilciye sağlanır. Aşağıdaki örnekte, arayan isteğe bağlı olarak belirtir:

   * sağlık kontrol`name`adı ( ). Eğer `null` `example_health_check` , kullanılır.
   * sistem durumu denetimi için`data1`dize veri noktası ( ).
   * sistem durumu denetimi için sonda`data2`veri noktası ( ). Eğer `null` `1` , kullanılır.
   * hata durumu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ). Varsayılan değer: `null`. If `null`, [HealthStatus.Sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bir başarısızlık durumu için bildirilir.
   * etiketleri`IEnumerable<string>`( ).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Sağlık Kontrolü Yayıncı

Servis <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> konteynerine bir şey eklendiğinde, sağlık kontrol sistemi sağlık `PublishAsync` kontrollerinizi düzenli aralıklarla yürütür ve sonuçla birlikte çağrıları yerine getirmez. Bu, her işlemin sistem durumunu belirlemek için izleme sistemini periyodik olarak aramasını bekleyen push tabanlı sistem senaryosunda yararlıdır.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Arabirimin tek bir yöntemi vardır:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>ayarlamanızı sağlar:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Örnekleri yürütmeden önce uygulama başladıktan sonra uygulanan ilk <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> gecikme. Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemeler için geçerli değildir. Varsayılan değer beş saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> süresi. Varsayılan değer 30 saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> Varsa `null` (varsayılan), sistem durumu denetimi yayımcısı hizmeti tüm kayıtlı sistem durumu denetimlerini çalıştırAr. Sistem durumu denetimlerinin bir alt kümesini çalıştırmak için, denetim kümesini filtreleyen bir işlev sağlayın. Yüklem her dönem değerlendirilir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnekler için sistem durumu denetimlerini yürütmek için zaman arası. Bir <xref:System.Threading.Timeout.InfiniteTimeSpan> zaman arası olmadan yürütmek için kullanın. Varsayılan değer 30 saniyedir.

> [!WARNING]
> ASP.NET Core 2.2 sürümünde, ayar <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulama tarafından onurlandırılan değildir; değerini <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>ayarlar. Bu sorun ASP.NET Core 3.0 adresinde ele alınmıştır.

Örnek uygulamada, `ReadinessPublisher` bir <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulamadır. Sistem durumu denetimi durumu her denetim için aşağıdakilerden biri olarak kaydedilir:

* Bilgi<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) sağlık kontrolleri <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>durumu ise .
* Hata<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) durum <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ya <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>da .

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazırlık denetiminde iki saniyelik bir başlatma gecikmesi vardır ve denetimi her 30 saniyede bir çalıştırın. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Uygulamayı etkinleştirmek için, örnek `ReadinessPublisher` bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection) konteynerinde tek tonlu bir hizmet olarak kaydedilir:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Aşağıdaki geçici çözüm, uygulamaya <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> zaten bir veya daha fazla barındırılan hizmet eklendiğinde hizmet kapsayıcısına bir örnek eklenmesine izin verir. Bu geçici çözüm Core 3.0 ASP.NET gerekli değildir.
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) [Uygulama Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayıncılar içerir.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="restrict-health-checks-with-mapwhen"></a>MapWhen ile sistem durumu denetimlerini kısıtlama

Sistem <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> durumu denetimi uç noktaları için istek ardışık dallandırmak için kullanın.

Aşağıdaki örnekte, `MapWhen` `api/HealthCheck` bitiş noktası için bir GET isteği alınırsa, Sağlık Denetimleri Ara ware etkinleştirmek için istek ardışık dalları:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
