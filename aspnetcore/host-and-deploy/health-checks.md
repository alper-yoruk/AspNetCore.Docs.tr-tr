---
title: ASP.NET Core durum denetimleri
author: rick-anderson
description: Uygulamalar ve veritabanları gibi ASP.NET Core altyapısı için sistem durumu denetimlerini ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/health-checks
ms.openlocfilehash: 67ba564068e8fba320ee04a59721052075337c65
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776070"
---
# <a name="health-checks-in-aspnet-core"></a>ASP.NET Core durum denetimleri

By [Glenn CONDRON](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu raporlamak için sistem durumu denetimleri ve kitaplıkları sunar.

Sistem durumu denetimleri, bir uygulama tarafından HTTP uç noktaları olarak gösterilir. Durum denetimi uç noktaları, çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:

* Sistem durumu araştırmaları, kapsayıcı yöneticileri ve yük dengeleyiciler tarafından bir uygulamanın durumunu denetlemek için kullanılabilir. Örneğin, bir kapsayıcı Orchestrator, sıralı bir dağıtımı kaldırarak veya kapsayıcıyı yeniden başlatarak başarısız olan bir sistem durumu denetimine yanıt verebilir. Yük dengeleyici, trafiği başarısız olan örnekten sağlıklı bir örneğe yönlendirerek sağlıklı olmayan bir uygulamaya tepki verebilir.
* Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı bir durum için izlenebilir.
* Sistem durumu denetimleri, kullanılabilirliği ve normal çalışmayı onaylamak için bir uygulamanın veritabanları ve dış hizmet uç noktaları gibi bağımlılıklarını test edebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama, bu konuda açıklanan senaryoların örneklerini içerir. Örnek uygulamayı belirli bir senaryo için çalıştırmak için, bir komut kabuğunda projenin klasöründen [DotNet Run](/dotnet/core/tools/dotnet-run) komutunu kullanın. Örnek uygulamayı kullanma hakkında ayrıntılı bilgi için bu konudaki örnek uygulamanın *README.MD* dosyasına ve senaryo açıklamalarına bakın.

## <a name="prerequisites"></a>Önkoşullar

Durum denetimleri, genellikle bir uygulamanın durumunu denetlemek için bir dış izleme hizmeti veya kapsayıcı Orchestrator ile birlikte kullanılır. Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağını belirleyin. İzleme sistemi ne tür bir sistem durumu denetimi oluşturulacağını ve bunların uç noktalarını nasıl yapılandıracağınızı belirler.

[Microsoft. AspNetCore. Diagnostics. Healthdenetimlerin](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketine ASP.NET Core uygulamaları için örtük olarak başvurulur. Entity Framework Core kullanarak sistem durumu denetimleri gerçekleştirmek için, [Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) paketine bir paket başvurusu ekleyin.

Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek üzere başlangıç kodu sağlar. [Veritabanı araştırma](#database-probe) senaryosu, [Aspnetcore. Diagnostics. healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının sistem durumunu denetler. [DbContext araştırma](#entity-framework-core-dbcontext-probe) senaryosu bir EF Core `DbContext`kullanarak bir veritabanını denetler. Örnek uygulama olan veritabanı senaryolarını araştırmak için:

* Bir veritabanı oluşturur ve bunun bağlantı dizesini *appSettings. JSON* dosyasında sağlar.
* , Proje dosyasında aşağıdaki paket başvurularına sahiptir:
  * [AspNetCore. Healthdenetimleri. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

Başka bir sistem durumu denetimi senaryosunda, sistem durumu denetimlerinin bir yönetim bağlantı noktasına nasıl filtreleneceği gösterilir. Örnek uygulama, Yönetim URL 'sini ve yönetim bağlantı noktasını içeren bir *Özellikler/launchSettings. JSON* dosyası oluşturmanızı gerektirir. Daha fazla bilgi için, [bağlantı noktasına göre filtrele](#filter-by-port) bölümüne bakın.

## <a name="basic-health-probe"></a>Temel sistem durumu araştırması

Birçok uygulama için, uygulamanın istekleri işleme için kullanılabilirliğini raporlayan temel bir durum araştırma yapılandırması, uygulamanın durumunu öğrenmek*liveness*için yeterlidir.

Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu denetimleri ara yazılımını çağırarak bir URL uç noktasında bir sistem durumu yanıtı ile yanıt verir. Varsayılan olarak, belirli bir bağımlılığı veya alt sistemi test etmek için belirli bir sistem durumu denetimi kayıtlı değildir. Sistem durumu uç nokta URL 'sinde yanıt veriyorsa, uygulama sağlıklı olarak değerlendirilir. Varsayılan yanıt yazıcı<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>, durumu () istemciye geri düz metin yanıtı olarak yazar [. sağlıklı](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)durum, sistem sağlığı durumu [. düşürülmüş](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durum.

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. ' `MapHealthChecks` `Startup.Configure`İ çağırarak bir sistem durumu denetim uç noktası oluşturun.

Örnek uygulamada, sistem durumu denetimi uç noktası şurada `/health` oluşturulur (*BasicStartup.cs*):

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

Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Docker örneği

[Docker](xref:host-and-deploy/docker/index) , temel sistem durumu denetimi `HEALTHCHECK` yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilen bir yerleşik yönerge sunar:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Durum denetimleri oluşturma

Sistem durumu denetimleri, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> arabirimini uygulayarak oluşturulur. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Yöntemi, sistem durumunu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , `Healthy` `Degraded`veya `Unhealthy`olarak belirten bir döndürür. Sonuç yapılandırılabilir bir durum kodu ile düz metin yanıtı olarak yazılır (yapılandırma, [sistem durumu denetimi seçenekleri](#health-check-options) bölümünde açıklanmıştır). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, isteğe bağlı anahtar-değer çiftleri de döndürebilir.

Aşağıdaki `ExampleHealthCheck` sınıf, bir sistem durumu denetiminin yerleşimini gösterir. Durum denetimleri mantığı `CheckHealthAsync` yöntemine yerleştirilir. Aşağıdaki örnek, `healthCheckResultHealthy`öğesini olarak `true`bir kukla değişkenini ayarlar. Değeri `healthCheckResultHealthy` olarak `false`ayarlanırsa, [healthcheckresult. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum döndürülür.

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

## <a name="register-health-check-services"></a>Sistem durumu denetimi hizmetlerini Kaydet

`ExampleHealthCheck` Türü, içindeki <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices`ile sistem durumu denetimi hizmetlerine eklenir:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Aşağıdaki <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> örnekte gösterilen aşırı yükleme, durum denetimi bir hata bildirdiğinde hata<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>durumu () öğesini raporlamak üzere ayarlar. Hata durumu (varsayılan) olarak `null` ayarlandıysa, [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir. Bu aşırı yükleme, kitaplık yazarları için yararlı bir senaryodur. burada, sistem durumu denetimi uygulaması ayarı varsa, bir sistem durumu denetimi hatası oluştuğunda, kitaplık tarafından belirtilen başarısızlık durumu uygulama tarafından zorlanır.

*Etiketler* , sistem durumu denetimlerini filtrelemek için kullanılabilir ( [durum denetimleri filtreleme](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, bir Lambda işlevi de yürütebilir. Aşağıdaki örnekte, sistem durumu denetim adı olarak `Example` belirtilir ve denetim her zaman sağlıklı bir durum döndürür:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Bağımsız <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> değişkenleri bir sistem durumu denetimi uygulamasına geçirme çağrısı. Aşağıdaki örnekte, `TestHealthCheckWithArgs` çağrıldığında bir tamsayıyı ve Use <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> dizesini kabul eder:

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

`TestHealthCheckWithArgs`, uygulamaya geçirilen tamsayı `AddTypeActivatedCheck` ve dizeyle çağırarak kaydedilir:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Sistem durumu denetimleri yönlendirmeyi kullanma

' `Startup.Configure`De, `MapHealthChecks` uç nokta URL 'si veya göreli yol ile Endpoint Builder ' ı çağırın:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Konak gerektir

Sistem `RequireHost` durumu denetimi uç noktası için izin verilen bir veya daha fazla konağı belirtmek için çağırın. Konaklar, puni kodu yerine Unicode olmalıdır ve bir bağlantı noktası içerebilir. Bir koleksiyon sağlanmazsa, herhangi bir konak kabul edilir.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Daha fazla bilgi için, [bağlantı noktasına göre filtrele](#filter-by-port) bölümüne bakın.

### <a name="require-authorization"></a>Yetkilendirme gerektir

Sistem `RequireAuthorization` durumu denetimi istek uç noktasındaki yetkilendirme ara yazılımını çalıştırma çağrısı. `RequireAuthorization` Aşırı yükleme bir veya daha fazla yetkilendirme ilkesini kabul eder. Bir ilke sağlanmazsa, varsayılan yetkilendirme ilkesi kullanılır.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Kaynaklar Arası İstekleri (CORS) etkinleştirme

Bir tarayıcıdan el ile sistem durumu denetimleri gerçekleştirmek yaygın kullanım senaryosu olmasa da, CORS ara yazılımı sistem durumu denetimleri uç `RequireCors` noktaları çağırarak etkinleştirilebilir. `RequireCors` Aşırı yükleme bir CORS ilke Oluşturucu temsilcisini (`CorsPolicyBuilder`) veya bir ilke adını kabul eder. Bir ilke sağlanmazsa varsayılan CORS ilkesi kullanılır. Daha fazla bilgi için bkz. <xref:security/cors>.

## <a name="health-check-options"></a>Sistem durumu denetimi seçenekleri

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirmek için bir fırsat sağlayın:

* [Durum denetimlerini filtrele](#filter-health-checks)
* [HTTP durum kodunu özelleştirme](#customize-the-http-status-code)
* [Önbellek üstbilgilerini gösterme](#suppress-cache-headers)
* [Çıktıyı özelleştirme](#customize-output)

### <a name="filter-health-checks"></a>Durum denetimlerini filtrele

Varsayılan olarak, sistem durumu denetimleri ara yazılımı tüm kayıtlı sistem durumu denetimlerini çalıştırır. Bir sistem durumu denetimleri alt kümesini çalıştırmak için, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> seçeneğe Boole değeri döndüren bir işlev sağlayın. Aşağıdaki örnekte, `Bar` sistem durumu denetimi işlevin koşullu deyimindeki`bar_tag`etiketiyle () tarafından filtrelenir, burada `true` yalnızca sistem durumu denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşiyorsa `foo_tag` döndürülür. `baz_tag`

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

' `Startup.Configure`De, `Predicate` ' çubuk ' sistem durumu denetimini filtreler. Yalnızca Foo ve baz Execute.:

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

Sistem <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> durumunun http durum kodlarına eşlenmesini özelleştirmek için kullanın. Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir. Durum kodu değerlerini gereksinimlerinize uyacak şekilde değiştirin.

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

### <a name="suppress-cache-headers"></a>Önbellek üstbilgilerini gösterme

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem durumu denetimlerinin, yanıt önbelleğini engellemek için araştırma yanıtına HTTP üstbilgileri ekleyip eklemediğini denetler. Değer (varsayılan) `false` ise, ara yazılım, yanıt önbelleğe almayı engellemek için `Cache-Control`, `Expires`, ve `Pragma` üst bilgilerini ayarlar veya geçersiz kılar. Değer ise `true`, ara yazılım yanıtın önbellek üstbilgilerini değiştirmez.

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

İçinde `Startup.Configure`, yanıt yazmak Için [Healthcheckoptions. responsewriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) seçeneğini bir temsilci olarak ayarlayın:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Varsayılan temsilci, [HealthReport. Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değerine sahip en az bir düz metin yanıtı yazar. Aşağıdaki özel temsilciler özel bir JSON yanıtını çıktı.

Örnek uygulamadaki ilk örnek, nasıl kullanılacağını göstermektedir <xref:System.Text.Json?displayProperty=fullName>:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

İkinci örnek [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/)' ın nasıl kullanılacağını gösterir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

Örnek uygulamada `Newtonsoft.Json` , sürümünü etkinleştirmek `WriteResponse`için *CustomWriterStartup.cs* içindeki `SYSTEM_TEXT_JSON` [Önişlemci yönergesini](xref:index#preprocessor-directives-in-sample-code) not edin.

Durum denetimleri API 'SI, biçim, izleme sistemine özgü olan karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz. Önceki örneklerde gereken yanıtı gerektiği gibi özelleştirin. İle `System.Text.Json`JSON serileştirmesi hakkında daha fazla bilgi için bkz. [.net 'te JSON serileştirme ve seri durumdan çıkarma](/dotnet/standard/serialization/system-text-json-how-to).

## <a name="database-probe"></a>Veritabanı araştırması

Bir sistem durumu denetimi, veritabanının normal olarak yanıt verip vermediğini göstermek üzere Boole testi olarak çalışacak bir veritabanı sorgusu belirtebilir.

Örnek uygulama, SQL Server veritabanında bir sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamalar için bir sistem durumu denetim kitaplığı olan [Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır. `AspNetCore.Diagnostics.HealthChecks`veritabanına yönelik `SELECT 1` bağlantının sağlıklı olduğunu doğrulamak için veritabanında bir sorgu yürütür.

> [!WARNING]
> Bir sorgu ile bir veritabanı bağlantısı denetlerken, hızlı bir şekilde dönen bir sorgu seçin. Sorgu yaklaşımı, veritabanını aşırı yükleme ve performansını düşürmeye yönelik riski çalıştırır. Çoğu durumda, test sorgusunun çalıştırılması gerekli değildir. Yalnızca veritabanına başarılı bir bağlantı oluşturmak yeterlidir. Bir sorgu çalıştırmak için gerekli olduğunu fark ederseniz, gibi basit bir seçme sorgusu seçin `SELECT 1`.

[Aspnetcore. Healthdenetimlerin. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)öğesine bir paket başvurusu ekleyin.

Örnek uygulamanın *appSettings. JSON* dosyasında geçerli bir veritabanı bağlantı dizesi sağlayın. Uygulama, adında `HealthCheckSample`bir SQL Server veritabanı kullanır:

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. Örnek uygulama, `AddSqlServer` yöntemini veritabanının bağlantı dizesiyle (*DbHealthStartup.cs*) çağırır:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

' De `MapHealthChecks` `Startup.Configure`çağırarak bir sistem durumu denetim uç noktası oluşturulur:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Örnek uygulamayı kullanarak veritabanı araştırma senaryosunu çalıştırmak için, bir komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="entity-framework-core-dbcontext-probe"></a>DbContext araştırması Entity Framework Core

`DbContext` Denetim, uygulamanın bir EF Core `DbContext`için yapılandırılmış veritabanıyla iletişim kurabildiğini onaylar. `DbContext` Denetim şu uygulamalar için desteklenir:

* [Entity Framework (EF) Core](/ef/core/)kullanın.
* [Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)'a bir paket başvurusu ekleyin.

`AddDbContextCheck<TContext>`için bir sistem durumu denetimi kaydeder `DbContext`. `DbContext` , Yöntemi olarak olarak `TContext` sağlanır. Hata durumu, Etiketler ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.

Varsayılan olarak:

* EF Core `DbContextHealthCheck` `CanConnectAsync` yöntemini çağırır. Yöntem aşırı yüklerini kullanarak `AddDbContextCheck` sistem durumunu denetlerken hangi işlemin çalıştırılacağını özelleştirebilirsiniz.
* Sistem durumu denetiminin adı, `TContext` türün adıdır.

Örnek `AppDbContext` uygulamada, `AddDbContextCheck` içinde `Startup.ConfigureServices` bir hizmet olarak sağlanır ve (*DbContextHealthStartup.cs*) olarak kaydedilir:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

' De `MapHealthChecks` `Startup.Configure`çağırarak bir sistem durumu denetim uç noktası oluşturulur:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Örnek uygulamayı kullanarak `DbContext` araştırma senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde mevcut olmadığından emin olun. Veritabanı varsa, silin.

Komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario dbcontext
```

Uygulama çalıştıktan sonra, bir tarayıcıda `/health` uç noktaya istek yaparak sistem durumunu kontrol edin. Veritabanı ve `AppDbContext` yok, uygulama aşağıdaki yanıtı sağlar:

```
Unhealthy
```

Veritabanını oluşturmak için örnek uygulamayı tetikleyin. İçin `/createdatabase`bir istek yapın. Uygulama yanıt veriyor:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

`/health` Uç noktaya bir istek oluşturun. Veritabanı ve bağlam var, bu nedenle uygulama yanıt veriyor:

```
Healthy
```

Veritabanını silmek için örnek uygulamayı tetikleyin. İçin `/deletedatabase`bir istek yapın. Uygulama yanıt veriyor:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

`/health` Uç noktaya bir istek oluşturun. Uygulama sağlıksız bir yanıt sağlar:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Ayrı hazırlık ve lizlilik araştırmaları

Bazı barındırma senaryolarında iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:

* Uygulama çalışıyor ancak henüz istekleri almaya hazırlanma. Bu durum, uygulamanın *hazır olma*durumu.
* Uygulama çalışır ve isteklere yanıt verir. Bu durum, uygulamanın kullanım *koşullarına*göre yapılır.

Hazırlık denetimi genellikle uygulamanın tüm alt sistemlerinin ve kaynaklarının kullanılabilir olup olmadığını belirlemede daha kapsamlı ve zaman alıcı denetim kümesi gerçekleştirir. Bir onay işareti yalnızca uygulamanın istekleri işlemek için kullanılabilir olup olmadığını belirlemede hızlı bir denetim gerçekleştirir. Uygulama hazırlık denetimini geçirdikten sonra, pahalı hazırlık denetimleri&mdash;kümesi daha fazla denetim sağlamak için uygulamayı daha fazla denetleme gereksinimi yoktur.

Örnek uygulama, [barındırılan bir hizmette](xref:fundamentals/host/hosted-services)uzun süre çalışan başlatma görevinin tamamlandığını raporlamak için bir sistem durumu denetimi içerir. , `StartupHostedServiceHealthCheck` Barındırılan hizmetin uzun süre `StartupTaskCompleted`çalışan görevi bittiğinde olarak `true` ayarlayabilmesini sağlayan bir özelliği sunar (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Uzun süre çalışan arka plan görevi bir [barındırılan hizmet](xref:fundamentals/host/hosted-services) (*Hizmetler/startuphostedservice*) tarafından başlatılır. Görevin `StartupHostedServiceHealthCheck.StartupTaskCompleted` sonunda şu şekilde `true`ayarlanır:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Sistem durumu denetimi barındırılan hizmetle birlikte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ' `Startup.ConfigureServices` de kaydedilir. Barındırılan hizmetin, sistem durumu denetiminde özelliği ayarlaması gerektiğinden, sistem durumu denetimi de hizmet kapsayıcısına kaydedilir (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

' De `MapHealthChecks` `Startup.Configure`çağırarak bir sistem durumu denetim uç noktası oluşturulur. Örnek uygulamada, sistem durumu denetimi uç noktaları şu konumda oluşturulur:

* `/health/ready`Hazırlık denetimi için. Hazır olma durumu denetimi, sistem durumu denetimine, `ready` etiketiyle sistem durumunu denetler.
* `/health/live`göz atın. `StartupHostedServiceHealthCheck` Libu denetim `false` , [Healthcheckoptions. koşula](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) dönerek tarafından filtreleneceğini denetler (daha fazla bilgi için bkz. [filtre durumu denetimleri](#filter-health-checks))

Aşağıdaki örnek kodda:

* Hazır olma denetimi ' hazır ' etiketiyle tüm kayıtlı denetimleri kullanır.
* , `Predicate` Tüm denetimleri dışlar ve 200-Tamam döndürür.

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

Örnek uygulamayı kullanarak hazırlık/lizlilik yapılandırma senaryosunu çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:

```dotnetcli
dotnet run --scenario liveness
```

Bir tarayıcıda, 15 saniye `/health/ready` geçtikten sonra birkaç kez ziyaret edin. Sistem durumu denetimi ilk 15 saniye boyunca *sağlıksız* durum bildiriyor. 15 saniye sonra, uç nokta, barındırılan hizmet tarafından uzun süre çalışan görevin tamamlandığını yansıtan *sağlıklı*şekilde raporlar.

Bu örnek ayrıca, iki saniyelik bir gecikmeyle ilk<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> hazırlık denetimini çalıştıran bir sistem durumu denetimi yayımcısı (uygulama) oluşturur. Daha fazla bilgi için bkz. [sistem durumu denetimi yayımcısı](#health-check-publisher) bölümü.

### <a name="kubernetes-example"></a>Kubernetes örneği

Ayrı hazır olma ve [Ezlilik denetimleri kullanmak, Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır. Kubernetes 'te, temel veritabanı kullanılabilirliğinin bir testi gibi istekleri kabul etmeden önce, zaman alan Başlangıç işlerini gerçekleştirmek için bir uygulamanın olması gerekebilir. Ayrı denetimlerin kullanılması, Orchestrator 'ın uygulamanın çalışıp çalışmadığını, ancak henüz hazırlanmadığını ya da uygulamanın başlayamadığını ayırt etmesine olanak tanır. Kubernetes 'te hazır olma ve Elanma araştırmaları hakkında daha fazla bilgi için bkz. Kubernetes belgelerindeki [limize ve hazırlık araştırmalarını yapılandırma](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) .

Aşağıdaki örnekte bir Kubernetes Readiness araştırma yapılandırması gösterilmektedir:

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Özel bir yanıt yazıcı ile ölçüm tabanlı araştırma

Örnek uygulama, özel bir yanıt yazıcısı ile bir bellek durumu denetimini gösterir.

`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) kullanıyorsa, düzeyi düşürülmüş bir durum bildirir. , <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Uygulama Için çöp TOPLAYıCı (GC) bilgilerini içerir (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. Durum denetimini öğesine <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` geçirerek etkinleştirmek yerine, hizmet olarak kaydedilir. Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler, sistem durumu denetimi Hizmetleri ve ara yazılım tarafından kullanılabilir. Sistem durumu denetimi hizmetlerini tek hizmet olarak kaydetmeyi öneririz.

Örnek uygulamanın *CustomWriterStartup.cs* ' de:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

' De `MapHealthChecks` `Startup.Configure`çağırarak bir sistem durumu denetim uç noktası oluşturulur. Sistem `WriteResponse` durumu denetimi yürütüldüğünde özel bir JSON yanıtının çıkışı için Microsoft. aspnetcore. Diagnostics. Healthdenetimleri. HealthCheckOptions. responsewriter> özelliği <bir temsilci sağlanır:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

`WriteResponse` Temsilci, `CompositeHealthCheckResult` bir JSON nesnesi halinde biçimlendirir ve sistem durumu denetimi yanıtı için JSON çıktısı verir. Daha fazla bilgi için [çıktıyı özelleştirme](#customize-output) bölümüne bakın.

Örnek uygulamayı kullanarak özel yanıt yazıcı çıkışıyla ölçüm tabanlı araştırmayı çalıştırmak için, komut kabuğu 'ndaki projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , disk depolama ve maksimum değer düzeyi denetimleri dahil ölçüm tabanlı sistem durumu denetimi senaryolarını içerir.
>
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="filter-by-port"></a>Bağlantı noktasına göre filtrele

Belirtilen `RequireHost` bağlantı `MapHealthChecks` noktasına sistem durumu denetim isteklerini kısıtlamak için bir bağlantı noktası belirten bir URL düzeniyle üzerinde arama yapın. Bu genellikle bir kapsayıcı ortamında, izleme hizmetleri için bir bağlantı noktası oluşturmak için kullanılır.

Örnek uygulama, [ortam değişkeni yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables-configuration-provider)kullanarak bağlantı noktasını yapılandırır. Bağlantı noktası, *Launchsettings. JSON* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına geçirilir. Ayrıca, sunucuyu Yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.

Yönetim bağlantı noktası yapılandırmasını göstermek üzere örnek uygulamayı kullanmak için, bir *Özellikler* klasöründe *launchsettings. JSON* dosyasını oluşturun.

Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings. JSON* dosyası, örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulması gerekir:

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

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. ' `MapHealthChecks` `Startup.Configure`İ çağırarak bir sistem durumu denetim uç noktası oluşturun.

Örnek uygulamada, içindeki `RequireHost` `Startup.Configure` uç noktada öğesine yapılan bir çağrı, yapılandırmadan yönetim bağlantı noktasını belirtir:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Uç noktalar içindeki `Startup.Configure`örnek uygulamada oluşturulur. Aşağıdaki örnek kodda:

* Hazır olma denetimi ' hazır ' etiketiyle tüm kayıtlı denetimleri kullanır.
* , `Predicate` Tüm denetimleri dışlar ve 200-Tamam döndürür.

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
> Yönetim bağlantı noktasını kodda açıkça ayarlayarak, örnek uygulamada *Launchsettings. JSON* dosyasını oluşturmaktan kaçınabilirsiniz. Uygulamasının oluşturulduğu *program.cs* <xref:Microsoft.Extensions.Hosting.HostBuilder> içinde, için <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> bir çağrı ekleyin ve uygulamanın yönetim bağlantı noktası uç noktasını sağlayın. ManagementPortStartup.cs `Configure` ' *ManagementPortStartup.cs*de, ile `RequireHost`yönetim bağlantı noktasını belirtin:
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

Yönetim bağlantı noktası yapılandırma senaryosunu örnek uygulamayı kullanarak çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Bir sistem durumu denetim kitaplığı dağıtma

Bir sistem durumu denetimini kitaplık olarak dağıtmak için:

1. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> Arabirimi tek başına sınıf olarak uygulayan bir sistem durumu denetimi yazın. Sınıf, yapılandırma verilerine erişmek için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection), tür etkinleştirme ve [adlandırılmış seçenekleri](xref:fundamentals/configuration/options) kullanabilir.

   Sistem durumu denetimleri mantığı `CheckHealthAsync`:

   * `data1`ve `data2` araştırma sistem durumu denetimi mantığını çalıştırmak için yönteminde kullanılır.
   * `AccessViolationException`işlenir.

   Bir <xref:System.AccessViolationException> gerçekleştiğinde <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> , kullanıcıların sistem durumu denetimleri hata durumunu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> yapılandırmasına izin vermek için ile döndürülür.

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

1. Kullanan uygulamanın `Startup.Configure` yöntemi içinde çağırdığı parametrelere sahip bir genişletme yöntemi yazın. Aşağıdaki örnekte, aşağıdaki sistem durumu denetim yöntemi imzasını varsayın:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Yukarıdaki imza, `ExampleHealthCheck` durumunun sistem durumu denetimi araştırma mantığını işlemek için ek veriler gerektirdiğini gösterir. Veriler, sistem durumu denetimi bir genişletme yöntemiyle kaydedildiğinde sistem durumu denetim örneğini oluşturmak için kullanılan temsilciye sağlanır. Aşağıdaki örnekte, çağıran isteğe bağlı olarak şunları belirtir:

   * sistem durumu denetim adı`name`(). İse `null`, `example_health_check` kullanılır.
   * sistem durumu denetimi (`data1`) için dize veri noktası.
   * sistem durumu denetimi (`data2`) için tamsayı veri noktası. İse `null`, `1` kullanılır.
   * hata durumu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Varsayılan değer: `null`. `null`Bir hata durumu için [HealthStatus. sağlıksız durum](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.
   * Etiketler (`IEnumerable<string>`).

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

## <a name="health-check-publisher"></a>Sistem durumu denetimi yayımcısı

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Hizmet kapsayıcısına eklendiğinde, sistem durumu denetimi sistemi düzenli olarak sistem durumu denetim ve çağrılarınızı `PublishAsync` yürütülür. Bu, her bir işlemin sistem durumunu belirlemede düzenli aralıklarla izleme sistemini çağırmasını bekleyen, gönderim tabanlı bir sistem durumu izleme sistemi senaryosunda yararlıdır.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Arabirim tek bir yönteme sahiptir:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>şunları ayarlamanıza izin verir:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Örnekleri yürütmeden <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> önce uygulama başladıktan sonra uygulanan ilk gecikme. Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemelere uygulanmaz. Varsayılan değer beş saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Varsayılan değer 30 saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; (Varsayılan) ise, sistem durumu denetimi yayımcı hizmeti tüm kayıtlı sistem durumu denetimlerini <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` çalıştırır. Bir sistem durumu denetimleri alt kümesini çalıştırmak için denetim kümesini filtreleyen bir işlev sağlayın. Koşul her dönem değerlendirilir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnekler için sistem durumu denetimlerini yürütmeye yönelik zaman aşımı. Zaman <xref:System.Threading.Timeout.InfiniteTimeSpan> aşımı olmadan yürütmek için kullanın. Varsayılan değer 30 saniyedir.

Örnek uygulamada, `ReadinessPublisher` bir <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulamadır. Sistem durumu denetimi durumu her denetim için günlük düzeyinde günlüğe kaydedilir:

* Durum denetim<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>durumu ise, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>bilgi ().
* Durum ya<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>da ise hata ().

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazır olma denetimi iki saniyelik başlangıç gecikmesine sahiptir ve denetimi her 30 saniyede bir çalıştırır. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Uygulamayı etkinleştirmek için örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında tek bir hizmet olarak kaydedilir `ReadinessPublisher` :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , [Application Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayımcılar içerir.
>
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="restrict-health-checks-with-mapwhen"></a>Durum denetimlerini Mapperne zaman kısıtla

Durum <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> denetimi uç noktaları için istek ardışık düzenini koşullu olarak dallandırmak için kullanın.

Aşağıdaki örnekte, `MapWhen` `api/HealthCheck` uç nokta için bir get Isteği alındığında durum denetimleri ara yazılımını etkinleştirmek üzere istek ardışık düzenini dallandırır:

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

ASP.NET Core, uygulama altyapısı bileşenlerinin sistem durumunu raporlamak için sistem durumu denetimleri ve kitaplıkları sunar.

Sistem durumu denetimleri, bir uygulama tarafından HTTP uç noktaları olarak gösterilir. Durum denetimi uç noktaları, çeşitli gerçek zamanlı izleme senaryoları için yapılandırılabilir:

* Sistem durumu araştırmaları, kapsayıcı yöneticileri ve yük dengeleyiciler tarafından bir uygulamanın durumunu denetlemek için kullanılabilir. Örneğin, bir kapsayıcı Orchestrator, sıralı bir dağıtımı kaldırarak veya kapsayıcıyı yeniden başlatarak başarısız olan bir sistem durumu denetimine yanıt verebilir. Yük dengeleyici, trafiği başarısız olan örnekten sağlıklı bir örneğe yönlendirerek sağlıklı olmayan bir uygulamaya tepki verebilir.
* Bellek, disk ve diğer fiziksel sunucu kaynaklarının kullanımı sağlıklı bir durum için izlenebilir.
* Sistem durumu denetimleri, kullanılabilirliği ve normal çalışmayı onaylamak için bir uygulamanın veritabanları ve dış hizmet uç noktaları gibi bağımlılıklarını test edebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama, bu konuda açıklanan senaryoların örneklerini içerir. Örnek uygulamayı belirli bir senaryo için çalıştırmak için, bir komut kabuğunda projenin klasöründen [DotNet Run](/dotnet/core/tools/dotnet-run) komutunu kullanın. Örnek uygulamayı kullanma hakkında ayrıntılı bilgi için bu konudaki örnek uygulamanın *README.MD* dosyasına ve senaryo açıklamalarına bakın.

## <a name="prerequisites"></a>Önkoşullar

Durum denetimleri, genellikle bir uygulamanın durumunu denetlemek için bir dış izleme hizmeti veya kapsayıcı Orchestrator ile birlikte kullanılır. Bir uygulamaya sistem durumu denetimleri eklemeden önce, hangi izleme sisteminin kullanılacağını belirleyin. İzleme sistemi ne tür bir sistem durumu denetimi oluşturulacağını ve bunların uç noktalarını nasıl yapılandıracağınızı belirler.

Microsoft. aspnetcore [. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Aspnetcore. Diagnostics. healthdenetimlerin](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) paketine bir paket başvurusu ekleyin.

Örnek uygulama, çeşitli senaryolar için sistem durumu denetimlerini göstermek üzere başlangıç kodu sağlar. [Veritabanı araştırma](#database-probe) senaryosu, [Aspnetcore. Diagnostics. healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanarak bir veritabanı bağlantısının sistem durumunu denetler. [DbContext araştırma](#entity-framework-core-dbcontext-probe) senaryosu bir EF Core `DbContext`kullanarak bir veritabanını denetler. Örnek uygulama olan veritabanı senaryolarını araştırmak için:

* Bir veritabanı oluşturur ve bunun bağlantı dizesini *appSettings. JSON* dosyasında sağlar.
* , Proje dosyasında aşağıdaki paket başvurularına sahiptir:
  * [AspNetCore. Healthdenetimleri. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

Başka bir sistem durumu denetimi senaryosunda, sistem durumu denetimlerinin bir yönetim bağlantı noktasına nasıl filtreleneceği gösterilir. Örnek uygulama, Yönetim URL 'sini ve yönetim bağlantı noktasını içeren bir *Özellikler/launchSettings. JSON* dosyası oluşturmanızı gerektirir. Daha fazla bilgi için, [bağlantı noktasına göre filtrele](#filter-by-port) bölümüne bakın.

## <a name="basic-health-probe"></a>Temel sistem durumu araştırması

Birçok uygulama için, uygulamanın istekleri işleme için kullanılabilirliğini raporlayan temel bir durum araştırma yapılandırması, uygulamanın durumunu öğrenmek*liveness*için yeterlidir.

Temel yapılandırma, sistem durumu denetimi hizmetlerini kaydeder ve sistem durumu denetimleri ara yazılımını çağırarak bir URL uç noktasında bir sistem durumu yanıtı ile yanıt verir. Varsayılan olarak, belirli bir bağımlılığı veya alt sistemi test etmek için belirli bir sistem durumu denetimi kayıtlı değildir. Sistem durumu uç nokta URL 'sinde yanıt veriyorsa, uygulama sağlıklı olarak değerlendirilir. Varsayılan yanıt yazıcı<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>, durumu () istemciye geri düz metin yanıtı olarak yazar [. sağlıklı](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)durum, sistem sağlığı durumu [. düşürülmüş](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) veya [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) durum.

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. İstek işleme ardışık düzeninde bulunan <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> sistem durumu denetimleri ara yazılımı için bir uç nokta `Startup.Configure`ekleyin.

Örnek uygulamada, sistem durumu denetimi uç noktası şurada `/health` oluşturulur (*BasicStartup.cs*):

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

Örnek uygulamayı kullanarak temel yapılandırma senaryosunu çalıştırmak için, komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Docker örneği

[Docker](xref:host-and-deploy/docker/index) , temel sistem durumu denetimi `HEALTHCHECK` yapılandırmasını kullanan bir uygulamanın durumunu denetlemek için kullanılabilen bir yerleşik yönerge sunar:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Durum denetimleri oluşturma

Sistem durumu denetimleri, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> arabirimini uygulayarak oluşturulur. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Yöntemi, sistem durumunu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , `Healthy` `Degraded`veya `Unhealthy`olarak belirten bir döndürür. Sonuç yapılandırılabilir bir durum kodu ile düz metin yanıtı olarak yazılır (yapılandırma, [sistem durumu denetimi seçenekleri](#health-check-options) bölümünde açıklanmıştır). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, isteğe bağlı anahtar-değer çiftleri de döndürebilir.

### <a name="example-health-check"></a>Örnek sistem durumu denetimi

Aşağıdaki `ExampleHealthCheck` sınıf, bir sistem durumu denetiminin yerleşimini gösterir. Durum denetimleri mantığı `CheckHealthAsync` yöntemine yerleştirilir. Aşağıdaki örnek, `healthCheckResultHealthy`öğesini olarak `true`bir kukla değişkenini ayarlar. Değeri `healthCheckResultHealthy` olarak `false`ayarlanırsa, [healthcheckresult. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) durum döndürülür.

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

### <a name="register-health-check-services"></a>Sistem durumu denetimi hizmetlerini Kaydet

`ExampleHealthCheck` Türü, ile `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>içindeki sistem durumu denetimi hizmetlerine eklenir:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Aşağıdaki <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> örnekte gösterilen aşırı yükleme, durum denetimi bir hata bildirdiğinde hata<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>durumu () öğesini raporlamak üzere ayarlar. Hata durumu (varsayılan) olarak `null` ayarlandıysa, [HealthStatus. sağlıksız](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir. Bu aşırı yükleme, kitaplık yazarları için yararlı bir senaryodur. burada, sistem durumu denetimi uygulaması ayarı varsa, bir sistem durumu denetimi hatası oluştuğunda, kitaplık tarafından belirtilen başarısızlık durumu uygulama tarafından zorlanır.

*Etiketler* , sistem durumu denetimlerini filtrelemek için kullanılabilir ( [durum denetimleri filtreleme](#filter-health-checks) bölümünde daha ayrıntılı olarak açıklanmıştır).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, bir Lambda işlevi de yürütebilir. Aşağıdaki `Startup.ConfigureServices` örnekte, sistem durumu denetim adı olarak `Example` belirtilir ve denetim her zaman sağlıklı bir durum döndürür:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Sistem durumu denetimleri ara yazılımı kullan

İçinde `Startup.Configure`, işlem <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> ardışık düzeninde uç nokta URL 'si veya göreli yol ile çağrı yapın:

```csharp
app.UseHealthChecks("/health");
```

Durum denetimlerinin belirli bir bağlantı noktasını dinlemesi gerekiyorsa, bağlantı noktasını ayarlamak <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> için bir aşırı yüklemesi kullanın ( [bağlantı noktasına göre filtrele](#filter-by-port) bölümünde anlatılmıştır):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Sistem durumu denetimi seçenekleri

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>sistem durumu denetimi davranışını özelleştirmek için bir fırsat sağlayın:

* [Durum denetimlerini filtrele](#filter-health-checks)
* [HTTP durum kodunu özelleştirme](#customize-the-http-status-code)
* [Önbellek üstbilgilerini gösterme](#suppress-cache-headers)
* [Çıktıyı özelleştirme](#customize-output)

### <a name="filter-health-checks"></a>Durum denetimlerini filtrele

Varsayılan olarak, sistem durumu denetimleri ara yazılımı tüm kayıtlı sistem durumu denetimlerini çalıştırır. Bir sistem durumu denetimleri alt kümesini çalıştırmak için, <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> seçeneğe Boole değeri döndüren bir işlev sağlayın. Aşağıdaki örnekte, `Bar` sistem durumu denetimi işlevin koşullu deyimindeki`bar_tag`etiketiyle () tarafından filtrelenir, burada `true` yalnızca sistem durumu denetiminin <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> özelliği eşleşiyorsa `foo_tag` döndürülür. `baz_tag`

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

Sistem <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> durumunun http durum kodlarına eşlenmesini özelleştirmek için kullanın. Aşağıdaki <xref:Microsoft.AspNetCore.Http.StatusCodes> atamalar, ara yazılım tarafından kullanılan varsayılan değerlerdir. Durum kodu değerlerini gereksinimlerinize uyacak şekilde değiştirin.

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

### <a name="suppress-cache-headers"></a>Önbellek üstbilgilerini gösterme

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Sistem durumu denetimlerinin, yanıt önbelleğini engellemek için araştırma yanıtına HTTP üstbilgileri ekleyip eklemediğini denetler. Değer (varsayılan) `false` ise, ara yazılım, yanıt önbelleğe almayı engellemek için `Cache-Control`, `Expires`, ve `Pragma` üst bilgilerini ayarlar veya geçersiz kılar. Değer ise `true`, ara yazılım yanıtın önbellek üstbilgilerini değiştirmez.

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

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Seçeneği, yanıtı yazmak için kullanılan bir temsilciyi alır veya ayarlar. Varsayılan temsilci, [HealthReport. Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değerine sahip en az bir düz metin yanıtı yazar.

`Startup.Configure` içinde:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Varsayılan temsilci, [HealthReport. Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)dize değerine sahip en az bir düz metin yanıtı yazar. Aşağıdaki özel temsilci `WriteResponse`, özel bir JSON yanıtı verir:

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

Durum denetimleri sistemi, karmaşık JSON dönüş biçimleri için yerleşik destek sağlamaz çünkü biçim, sizin tercih ettiğiniz izleme sistemine özgüdür. Gereksinimlerinizi karşılamak için gereken önceki `JObject` örnekteki ' i özelleştirebilirsiniz.

## <a name="database-probe"></a>Veritabanı araştırması

Bir sistem durumu denetimi, veritabanının normal olarak yanıt verip vermediğini göstermek üzere Boole testi olarak çalışacak bir veritabanı sorgusu belirtebilir.

Örnek uygulama, SQL Server veritabanında bir sistem durumu denetimi gerçekleştirmek için ASP.NET Core uygulamalar için bir sistem durumu denetim kitaplığı olan [Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)kullanır. `AspNetCore.Diagnostics.HealthChecks`veritabanına yönelik `SELECT 1` bağlantının sağlıklı olduğunu doğrulamak için veritabanında bir sorgu yürütür.

> [!WARNING]
> Bir sorgu ile bir veritabanı bağlantısı denetlerken, hızlı bir şekilde dönen bir sorgu seçin. Sorgu yaklaşımı, veritabanını aşırı yükleme ve performansını düşürmeye yönelik riski çalıştırır. Çoğu durumda, test sorgusunun çalıştırılması gerekli değildir. Yalnızca veritabanına başarılı bir bağlantı oluşturmak yeterlidir. Bir sorgu çalıştırmak için gerekli olduğunu fark ederseniz, gibi basit bir seçme sorgusu seçin `SELECT 1`.

[Aspnetcore. Healthdenetimlerin. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)öğesine bir paket başvurusu ekleyin.

Örnek uygulamanın *appSettings. JSON* dosyasında geçerli bir veritabanı bağlantı dizesi sağlayın. Uygulama, adında `HealthCheckSample`bir SQL Server veritabanı kullanır:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. Örnek uygulama, `AddSqlServer` yöntemini veritabanının bağlantı dizesiyle (*DbHealthStartup.cs*) çağırır:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

İçindeki `Startup.Configure`uygulama işleme ardışık düzeninde bulunan sistem durumu denetimleri ara yazılımı:

```csharp
app.UseHealthChecks("/health");
```

Örnek uygulamayı kullanarak veritabanı araştırma senaryosunu çalıştırmak için, bir komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="entity-framework-core-dbcontext-probe"></a>DbContext araştırması Entity Framework Core

`DbContext` Denetim, uygulamanın bir EF Core `DbContext`için yapılandırılmış veritabanıyla iletişim kurabildiğini onaylar. `DbContext` Denetim şu uygulamalar için desteklenir:

* [Entity Framework (EF) Core](/ef/core/)kullanın.
* [Microsoft. Extensions. Diagnostics. Healthdenetimleri. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)'a bir paket başvurusu ekleyin.

`AddDbContextCheck<TContext>`için bir sistem durumu denetimi kaydeder `DbContext`. `DbContext` , Yöntemi olarak olarak `TContext` sağlanır. Hata durumu, Etiketler ve özel bir test sorgusunu yapılandırmak için aşırı yükleme kullanılabilir.

Varsayılan olarak:

* EF Core `DbContextHealthCheck` `CanConnectAsync` yöntemini çağırır. Yöntem aşırı yüklerini kullanarak `AddDbContextCheck` sistem durumunu denetlerken hangi işlemin çalıştırılacağını özelleştirebilirsiniz.
* Sistem durumu denetiminin adı, `TContext` türün adıdır.

Örnek `AppDbContext` uygulamada, `AddDbContextCheck` içinde `Startup.ConfigureServices` bir hizmet olarak sağlanır ve (*DbContextHealthStartup.cs*) olarak kaydedilir:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Örnek uygulamada, `UseHealthChecks` Içindeki `Startup.Configure`sistem durumu denetimleri ara yazılımını ekler.

```csharp
app.UseHealthChecks("/health");
```

Örnek uygulamayı kullanarak `DbContext` araştırma senaryosunu çalıştırmak için, bağlantı dizesi tarafından belirtilen veritabanının SQL Server örneğinde mevcut olmadığından emin olun. Veritabanı varsa, silin.

Komut kabuğunda projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario dbcontext
```

Uygulama çalıştıktan sonra, bir tarayıcıda `/health` uç noktaya istek yaparak sistem durumunu kontrol edin. Veritabanı ve `AppDbContext` yok, uygulama aşağıdaki yanıtı sağlar:

```
Unhealthy
```

Veritabanını oluşturmak için örnek uygulamayı tetikleyin. İçin `/createdatabase`bir istek yapın. Uygulama yanıt veriyor:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

`/health` Uç noktaya bir istek oluşturun. Veritabanı ve bağlam var, bu nedenle uygulama yanıt veriyor:

```
Healthy
```

Veritabanını silmek için örnek uygulamayı tetikleyin. İçin `/deletedatabase`bir istek yapın. Uygulama yanıt veriyor:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

`/health` Uç noktaya bir istek oluşturun. Uygulama sağlıksız bir yanıt sağlar:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Ayrı hazırlık ve lizlilik araştırmaları

Bazı barındırma senaryolarında iki uygulama durumunu ayırt eden bir çift sistem durumu denetimi kullanılır:

* Uygulama çalışıyor ancak henüz istekleri almaya hazırlanma. Bu durum, uygulamanın *hazır olma*durumu.
* Uygulama çalışır ve isteklere yanıt verir. Bu durum, uygulamanın kullanım *koşullarına*göre yapılır.

Hazırlık denetimi genellikle uygulamanın tüm alt sistemlerinin ve kaynaklarının kullanılabilir olup olmadığını belirlemede daha kapsamlı ve zaman alıcı denetim kümesi gerçekleştirir. Bir onay işareti yalnızca uygulamanın istekleri işlemek için kullanılabilir olup olmadığını belirlemede hızlı bir denetim gerçekleştirir. Uygulama hazırlık denetimini geçirdikten sonra, pahalı hazırlık denetimleri&mdash;kümesi daha fazla denetim sağlamak için uygulamayı daha fazla denetleme gereksinimi yoktur.

Örnek uygulama, [barındırılan bir hizmette](xref:fundamentals/host/hosted-services)uzun süre çalışan başlatma görevinin tamamlandığını raporlamak için bir sistem durumu denetimi içerir. , `StartupHostedServiceHealthCheck` Barındırılan hizmetin uzun süre `StartupTaskCompleted`çalışan görevi bittiğinde olarak `true` ayarlayabilmesini sağlayan bir özelliği sunar (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Uzun süre çalışan arka plan görevi bir [barındırılan hizmet](xref:fundamentals/host/hosted-services) (*Hizmetler/startuphostedservice*) tarafından başlatılır. Görevin `StartupHostedServiceHealthCheck.StartupTaskCompleted` sonunda şu şekilde `true`ayarlanır:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Sistem durumu denetimi barındırılan hizmetle birlikte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ' `Startup.ConfigureServices` de kaydedilir. Barındırılan hizmetin, sistem durumu denetiminde özelliği ayarlaması gerektiğinden, sistem durumu denetimi de hizmet kapsayıcısına kaydedilir (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

İçindeki `Startup.Configure`uygulama işleme ardışık düzeninde sistem durumu denetimleri ara yazılımı. Örnek uygulamada, sistem durumu denetimi uç noktaları, hazırlık denetimi ve `/health/ready` `/health/live` Liya denetimi için konumunda oluşturulur. Hazır olma durumu denetimi, sistem durumu denetimine, `ready` etiketiyle sistem durumunu denetler. `StartupHostedServiceHealthCheck` Libu denetim `false` , [Healthcheckoptions. koşula](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) dönerek tarafından filtreleneceğini denetler (daha fazla bilgi Için bkz. [sistem durumu denetimlerini filtreleme](#filter-health-checks)):

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

Örnek uygulamayı kullanarak hazırlık/lizlilik yapılandırma senaryosunu çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:

```dotnetcli
dotnet run --scenario liveness
```

Bir tarayıcıda, 15 saniye `/health/ready` geçtikten sonra birkaç kez ziyaret edin. Sistem durumu denetimi ilk 15 saniye boyunca *sağlıksız* durum bildiriyor. 15 saniye sonra, uç nokta, barındırılan hizmet tarafından uzun süre çalışan görevin tamamlandığını yansıtan *sağlıklı*şekilde raporlar.

Bu örnek ayrıca, iki saniyelik bir gecikmeyle ilk<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> hazırlık denetimini çalıştıran bir sistem durumu denetimi yayımcısı (uygulama) oluşturur. Daha fazla bilgi için bkz. [sistem durumu denetimi yayımcısı](#health-check-publisher) bölümü.

### <a name="kubernetes-example"></a>Kubernetes örneği

Ayrı hazır olma ve [Ezlilik denetimleri kullanmak, Kubernetes](https://kubernetes.io/)gibi bir ortamda yararlıdır. Kubernetes 'te, temel veritabanı kullanılabilirliğinin bir testi gibi istekleri kabul etmeden önce, zaman alan Başlangıç işlerini gerçekleştirmek için bir uygulamanın olması gerekebilir. Ayrı denetimlerin kullanılması, Orchestrator 'ın uygulamanın çalışıp çalışmadığını, ancak henüz hazırlanmadığını ya da uygulamanın başlayamadığını ayırt etmesine olanak tanır. Kubernetes 'te hazır olma ve Elanma araştırmaları hakkında daha fazla bilgi için bkz. Kubernetes belgelerindeki [limize ve hazırlık araştırmalarını yapılandırma](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) .

Aşağıdaki örnekte bir Kubernetes Readiness araştırma yapılandırması gösterilmektedir:

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Özel bir yanıt yazıcı ile ölçüm tabanlı araştırma

Örnek uygulama, özel bir yanıt yazıcısı ile bir bellek durumu denetimini gösterir.

`MemoryHealthCheck`uygulama belirli bir bellek eşiğini (örnek uygulamada 1 GB) kullanıyorsa sağlıksız bir durum bildirir. , <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Uygulama Için çöp TOPLAYıCı (GC) bilgilerini içerir (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. Durum denetimini öğesine <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` geçirerek etkinleştirmek yerine, hizmet olarak kaydedilir. Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> kayıtlı hizmetler, sistem durumu denetimi Hizmetleri ve ara yazılım tarafından kullanılabilir. Sistem durumu denetimi hizmetlerini tek hizmet olarak kaydetmeyi öneririz.

Örnek uygulamada (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

İçindeki `Startup.Configure`uygulama işleme ardışık düzeninde sistem durumu denetimleri ara yazılımı. Durum `WriteResponse` denetimi yürütüldüğünde özel bir JSON `ResponseWriter` yanıtının çıkışı için özelliğine bir temsilci sağlanır:

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

`WriteResponse` YÖNTEMI bir JSON nesnesi `CompositeHealthCheckResult` olarak biçimlendirir ve sistem durumu denetimi yanıtı için JSON çıktısı verir:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Örnek uygulamayı kullanarak özel yanıt yazıcı çıkışıyla ölçüm tabanlı araştırmayı çalıştırmak için, komut kabuğu 'ndaki projenin klasöründen aşağıdaki komutu yürütün:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [Aspnetcore. Diagnostics. healthcheck](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , disk depolama ve maksimum değer düzeyi denetimleri dahil ölçüm tabanlı sistem durumu denetimi senaryolarını içerir.
>
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="filter-by-port"></a>Bağlantı noktasına göre filtrele

Bir <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> bağlantı noktası ile çağırmak, sistem durumu denetimi isteklerini belirtilen bağlantı noktasına kısıtlar. Bu genellikle bir kapsayıcı ortamında, izleme hizmetleri için bir bağlantı noktası oluşturmak için kullanılır.

Örnek uygulama, [ortam değişkeni yapılandırma sağlayıcısını](xref:fundamentals/configuration/index#environment-variables-configuration-provider)kullanarak bağlantı noktasını yapılandırır. Bağlantı noktası, *Launchsettings. JSON* dosyasında ayarlanır ve bir ortam değişkeni aracılığıyla yapılandırma sağlayıcısına geçirilir. Ayrıca, sunucuyu Yönetim bağlantı noktasındaki istekleri dinleyecek şekilde yapılandırmanız gerekir.

Yönetim bağlantı noktası yapılandırmasını göstermek üzere örnek uygulamayı kullanmak için, bir *Özellikler* klasöründe *launchsettings. JSON* dosyasını oluşturun.

Örnek uygulamadaki aşağıdaki *Özellikler/launchSettings. JSON* dosyası, örnek uygulamanın proje dosyalarına dahil değildir ve el ile oluşturulması gerekir:

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

İle <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> sistem durumu denetimi hizmetlerini kaydedin `Startup.ConfigureServices`. İçin <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> yapılan çağrı, yönetim bağlantı noktasını belirtir (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Kod içinde açıkça URL 'Leri ve yönetim bağlantı noktasını ayarlayarak *Launchsettings. JSON* dosyasını örnek uygulamada oluşturmaktan kaçınabilirsiniz. *Program.cs* içinde oluşturulduğu yerde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> , için <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> bir çağrı ekleyin ve uygulamanın normal yanıt uç noktasını ve yönetim bağlantı noktası uç noktasını sağlayın. *ManagementPortStartup.cs* burada <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> çağrıldığında, yönetim bağlantı noktasını açıkça belirtin.
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

Yönetim bağlantı noktası yapılandırma senaryosunu örnek uygulamayı kullanarak çalıştırmak için, aşağıdaki komutu projenin klasöründen bir komut kabuğu 'ndan yürütün:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Bir sistem durumu denetim kitaplığı dağıtma

Bir sistem durumu denetimini kitaplık olarak dağıtmak için:

1. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> Arabirimi tek başına sınıf olarak uygulayan bir sistem durumu denetimi yazın. Sınıf, yapılandırma verilerine erişmek için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection), tür etkinleştirme ve [adlandırılmış seçenekleri](xref:fundamentals/configuration/options) kullanabilir.

   Sistem durumu denetimleri mantığı `CheckHealthAsync`:

   * `data1`ve `data2` araştırma sistem durumu denetimi mantığını çalıştırmak için yönteminde kullanılır.
   * `AccessViolationException`işlenir.

   Bir <xref:System.AccessViolationException> gerçekleştiğinde <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> , kullanıcıların sistem durumu denetimleri hata durumunu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> yapılandırmasına izin vermek için ile döndürülür.

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

1. Kullanan uygulamanın `Startup.Configure` yöntemi içinde çağırdığı parametrelere sahip bir genişletme yöntemi yazın. Aşağıdaki örnekte, aşağıdaki sistem durumu denetim yöntemi imzasını varsayın:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Yukarıdaki imza, `ExampleHealthCheck` durumunun sistem durumu denetimi araştırma mantığını işlemek için ek veriler gerektirdiğini gösterir. Veriler, sistem durumu denetimi bir genişletme yöntemiyle kaydedildiğinde sistem durumu denetim örneğini oluşturmak için kullanılan temsilciye sağlanır. Aşağıdaki örnekte, çağıran isteğe bağlı olarak şunları belirtir:

   * sistem durumu denetim adı`name`(). İse `null`, `example_health_check` kullanılır.
   * sistem durumu denetimi (`data1`) için dize veri noktası.
   * sistem durumu denetimi (`data2`) için tamsayı veri noktası. İse `null`, `1` kullanılır.
   * hata durumu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Varsayılan değer: `null`. `null`Bir hata durumu için [HealthStatus. sağlıksız durum](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) bildirilir.
   * Etiketler (`IEnumerable<string>`).

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

## <a name="health-check-publisher"></a>Sistem durumu denetimi yayımcısı

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Hizmet kapsayıcısına eklendiğinde, sistem durumu denetimi sistemi düzenli olarak sistem durumu denetim ve çağrılarınızı `PublishAsync` yürütülür. Bu, her bir işlemin sistem durumunu belirlemede düzenli aralıklarla izleme sistemini çağırmasını bekleyen, gönderim tabanlı bir sistem durumu izleme sistemi senaryosunda yararlıdır.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Arabirim tek bir yönteme sahiptir:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>şunları ayarlamanıza izin verir:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Örnekleri yürütmeden <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> önce uygulama başladıktan sonra uygulanan ilk gecikme. Gecikme başlangıçta bir kez uygulanır ve sonraki yinelemelere uygulanmaz. Varsayılan değer beş saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Varsayılan değer 30 saniyedir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; (Varsayılan) ise, sistem durumu denetimi yayımcı hizmeti tüm kayıtlı sistem durumu denetimlerini <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` çalıştırır. Bir sistem durumu denetimleri alt kümesini çalıştırmak için denetim kümesini filtreleyen bir işlev sağlayın. Koşul her dönem değerlendirilir.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Tüm <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> örnekler için sistem durumu denetimlerini yürütmeye yönelik zaman aşımı. Zaman <xref:System.Threading.Timeout.InfiniteTimeSpan> aşımı olmadan yürütmek için kullanın. Varsayılan değer 30 saniyedir.

> [!WARNING]
> ASP.NET Core 2,2 sürümünde, ayar <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulama tarafından kabul edilemez; değerini ayarlar <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Bu sorun ASP.NET Core 3,0 ' de giderilmiştir.

Örnek uygulamada, `ReadinessPublisher` bir <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> uygulamadır. Durum denetimi durumu her denetim için şu şekilde günlüğe kaydedilir:

* Durum denetim<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>durumu ise, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>bilgi ().
* Durum ya<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>da ise hata ().

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

Örnek uygulamanın `LivenessProbeStartup` örneğinde, `StartupHostedService` hazır olma denetimi iki saniyelik başlangıç gecikmesine sahiptir ve denetimi her 30 saniyede bir çalıştırır. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Uygulamayı etkinleştirmek için örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında tek bir hizmet olarak kaydedilir `ReadinessPublisher` :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Aşağıdaki geçici çözüm, uygulamaya bir <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> veya daha fazla barındırılan hizmet zaten eklenmiş olduğunda hizmet kapsayıcısına bir örnek eklenmesine izin verir. Bu geçici çözüm ASP.NET Core 3,0 ' de gerekli değildir.
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
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) , [Application Insights](/azure/application-insights/app-insights-overview)dahil olmak üzere çeşitli sistemler için yayımcılar içerir.
>
> [Aspnetcore. Diagnostics. Healthdenetimleri](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) Microsoft tarafından korunmaz veya desteklenmez.

## <a name="restrict-health-checks-with-mapwhen"></a>Durum denetimlerini Mapperne zaman kısıtla

Durum <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> denetimi uç noktaları için istek ardışık düzenini koşullu olarak dallandırmak için kullanın.

Aşağıdaki örnekte, `MapWhen` `api/HealthCheck` uç nokta için bir get Isteği alındığında durum denetimleri ara yazılımını etkinleştirmek üzere istek ardışık düzenini dallandırır:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
