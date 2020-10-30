---
title: IIS ve ASP.NET Core ile işlem dışı barındırma
author: rick-anderson
description: IIS ve ASP.NET Core modülüyle işlem dışı barındırma hakkında bilgi edinin.
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060423"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>IIS ve ASP.NET Core ile işlem dışı barındırma 

ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, ASP.NET Core modülü işlem yönetimini işler. Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![İşlem dışı barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-outofprocess.png)

1. İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır.
1. Sürücü, Web sitesinin yapılandırılmış bağlantı noktasında istekleri IIS 'ye yönlendirir. Yapılandırılmış bağlantı noktası genellikle 80 (HTTP) veya 443 (HTTPS).
1. Modül, isteği uygulama için rastgele bir bağlantı noktasında Kestrel 'e iletir. Rastgele bağlantı noktası 80 veya 443 değildir.

<!-- make this a bullet list -->
ASP.NET Core modülü başlatma sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>Uzantı, sunucuyu dinlemek üzere yapılandırır `http://localhost:{PORT}` . Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir. Modül HTTPS iletmeyi desteklemiyor. İstekler, HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.

Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine iletilir. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir. IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir. Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri iletilir.

ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..

Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="enable-the-iisintegration-components"></a>Iısıntegration bileşenlerini etkinleştirin

() İçinde bir konak `CreateHostBuilder` oluştururken `Program.cs` <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> IIS tümleştirmesini etkinleştirmek için çağırın:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/generic-host#default-builder-settings> ..


**İşlem dışı barındırma modeli**

IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır. İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |
| `ForwardClientCertificate`     | `true`  | `true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) ve ASP.NET Core modülü, iletmek üzere yapılandırılır:

* Düzen (HTTP/HTTPS).
* İsteğin kaynaklandığı uzak IP adresi.

[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , Iletilen üstbilgiler ara yazılımını yapılandırır.

Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini `OutOfProcess` Proje dosyasında () olarak ayarlayın `.csproj` :

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

İşlem içi barındırma `InProcess` , varsayılan değer olan ile ayarlanır.

Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.

IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .

İşlem dışı için şunu [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> :

* ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
* Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

### <a name="process-name"></a>İşlem adı

`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).

Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır. ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..

ASP.NET Core modülü de şunları yapabilir:

* Çalışan işlem için ortam değişkenlerini ayarlayın.
* Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.
* Windows kimlik doğrulama belirteçlerini ilet.
