---
title: IIS ve ASP.NET Core ile işlem içi barındırma
author: rick-anderson
description: IIS ve ASP.NET Core modülüyle Işlem içi barındırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058486"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>IIS ve ASP.NET Core ile işlem içi barındırma 

İşlem içi barındırma, IIS çalışan işlemiyle aynı işlemde bir ASP.NET Core uygulaması çalıştırır. İşlem içi barındırma, istek dışı barındırmak için gelişmiş performans sağlar çünkü istekler, giden ağ trafiği ile aynı makineye geri döndürülen bir ağ arabirimidir.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve süreçte barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![İşlem içi barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>İşlem içi barındırmayı etkinleştir

ASP.NET Core 3,0 ' den itibaren, işlem içi barındırma IIS 'e dağıtılan tüm uygulamalar için varsayılan olarak etkinleştirilmiştir.

Bir uygulamayı işlem içi barındırma için açıkça yapılandırmak üzere, `<AspNetCoreHostingModel>` özelliğinin değerini `InProcess` Proje dosyasında () olarak ayarlayın `.csproj` :

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Genel mimari

Bir isteğin genel akışı aşağıdaki gibidir:

1. Web 'den çekirdek modu HTTP.sys sürücüsüne bir istek ulaşır.
1. Sürücü, yerel isteği Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).
1. ASP.NET Core modülü yerel isteği alır ve IIS HTTP sunucusuna ( `IISHttpServer` ) geçirir. IIS HTTP sunucusu, isteği yerelden yönetilene dönüştüren bir IIS için işlem içi sunucu uygulamasıdır.

IIS HTTP sunucusu isteği tamamladıktan sonra:

1. İstek ASP.NET Core ara yazılım ardışık düzenine gönderilir.
1. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.
1. Uygulamanın yanıtı IIS HTTP sunucusu aracılığıyla IIS 'e geri geçirilir.
1. IIS yanıtı, isteği başlatan istemciye gönderir.

`CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.Server.IServer> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> [CoreCLR](/dotnet/standard/glossary#coreclr) 'YI önyüklemek ve uygulamayı IIS çalışan işleminin (veya) içinde barındırmak için yöntemini çağırarak bir örnek ekler `w3wp.exe` `iisexpress.exe` . Performans testleri, bir .NET Core uygulamasını işlem içinde barındırmanın, uygulamayı işlem dışı ve [Kestrel](xref:fundamentals/servers/kestrel)'e yönelik proxy istekleri barındırmakla karşılaştırıldığında önemli ölçüde daha yüksek istek aktarım hızı sağladığını gösterir.

Tek bir dosya yürütülebilir dosyası olarak yayınlanan uygulamalar, işlem içi barındırma modeliyle yüklenemez.

## <a name="application-configuration"></a>Uygulama yapılandırması

IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISServerOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Aşağıdaki örnek, Automatıcauthentication 'ı devre dışı bırakır:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Seçenek | Varsayılan | Ayar |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | `true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır. İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |
| `AllowSynchronousIO` | `false` | Ve için zaman uyumlu g/ç 'ye izin verilip verilmeyeceğini belirtir `HttpContext.Request` `HttpContext.Response` . |
| `MaxRequestBodySize` | `30000000` | İçin en büyük istek gövdesi boyutunu alır veya ayarlar `HttpRequest` . IIS 'nin `maxAllowedContentLength` , içindeki kümesinden önce işlenecek sınıra sahip olduğunu unutmayın `MaxRequestBodySize` `IISServerOptions` . ' Nin değiştirilmesi `MaxRequestBodySize` , etkilemez `maxAllowedContentLength` . Arttırmak için `maxAllowedContentLength` ' de bir giriş ekleyerek `web.config` `maxAllowedContentLength` daha yüksek bir değere ayarlayın. Daha fazla ayrıntı için bkz. [yapılandırma](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>İşlem içi ve işlem dışı barındırma arasındaki farklar

İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:

* Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) . İşlem içi için şunu [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> :

  * Kaydolun `IISHttpServer` .
  * ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
  * Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

* [ `requestTimeout` Özniteliği](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.

* Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez. Uygulama başına bir uygulama havuzu kullanın.

* Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir. Örneğin, 32 bit (x86) için yayımlanan uygulamalarda IIS uygulama havuzları için 32 bit etkinleştirilmiş olmalıdır. Daha fazla bilgi için [IIS sitesini oluşturma](xref:tutorials/publish-to-iis#create-the-iis-site) bölümüne bakın.

* İstemci bağlantısı kesiliyor algılandı. [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)İstemci bağlantısı kesildiğinde iptal belirteci iptal edilir.

* İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz. Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez. Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> kimlik doğrulama hizmetleri Ekle ' yi çağırın:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.
