---
title: ClaimsPrincipal. Current öğesinden geçir
author: mjrousos
description: Geçerli kimliği doğrulanmış kullanıcının kimliğini ve ASP.NET Core taleplerini almak için ClaimsPrincipal. Current ' dan uzağa geçiş yapmayı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 426fd90374a460cb283d0d3ba921e1312fb17940
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634078"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>ClaimsPrincipal. Current öğesinden geçir

ASP.NET 4. x projelerinde, geçerli kimliği doğrulanmış kullanıcının kimliğini ve taleplerini almak için [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) kullanımı yaygındır. ASP.NET Core, bu özellik artık ayarlı değildir. Kendisine bağlı olan kodun, geçerli kimliği doğrulanmış kullanıcının kimliğini farklı yollarla alması için güncelleştirilmesi gerekir.

## <a name="context-specific-state-instead-of-static-state"></a>Statik durum yerine içeriğe özgü durum

ASP.NET Core kullanılırken, her ikisi de değeri `ClaimsPrincipal.Current` `Thread.CurrentPrincipal` ayarlı değildir. Bu özelliklerin her ikisi de ASP.NET Core genellikle kaçınan statik durumu temsil eder. Bunun yerine, ASP.NET Core geçerli kullanıcının kimliği gibi bağımlılıklar sağlamak için [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kullanır. Test kimlikleri kolay bir şekilde eklenebilir olduğundan, geçerli kullanıcının kimliğini dı 'dan almak daha fazla sayıda olabilir.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>ASP.NET Core uygulamasındaki geçerli kullanıcıyı alma

Şu yerine, kimliği doğrulanmış geçerli kullanıcının ASP.NET Core alınması için birkaç seçenek vardır `ClaimsPrincipal` `ClaimsPrincipal.Current` :

* **ControllerBase. User**. MVC denetleyicileri, geçerli kimliği doğrulanmış kullanıcıya [Kullanıcı](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) özelliği ile erişebilir.
* **HttpContext. User**. Geçerli kullanıcıya erişimi olan bileşenler `HttpContext` (örneğin, ara yazılım), geçerli kullanıcıyı `ClaimsPrincipal` [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)öğesinden alabilir.
* **Çağırandan geçirildi**. Güncel erişimi olmayan kitaplıklar, `HttpContext` genellikle denetleyiciler veya ara yazılım bileşenlerinden çağırılır ve geçerli kullanıcının kimliği bir bağımsız değişken olarak geçirilebilir.
* **Ihttpcontextaccessor**. ASP.NET Core geçirilecek proje, geçerli kullanıcının kimliğini tüm gerekli konumlara kolayca geçiremeyecek kadar büyük olabilir. Bu gibi durumlarda, [ıhttpcontextaccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) geçici çözüm olarak kullanılabilir. `IHttpContextAccessor` geçerli `HttpContext` (varsa) erişebilir. Eğer dı kullanılıyorsa, bkz <xref:fundamentals/httpcontext> .. Geçerli kullanıcının kimliğini, ASP.NET Core dı odaklı mimarisiyle çalışacak şekilde güncelleştirilmemiş kodda almaya yönelik kısa süreli bir çözüm şu şekilde olacaktır:

  * `IHttpContextAccessor`' De [Addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793) 'ı çağırarak dı kapsayıcısında kullanılabilir hale getirin `Startup.ConfigureServices` .
  * Başlatma sırasında bir örneğini alın `IHttpContextAccessor` ve statik bir değişkende depolayın. Örnek, daha önce geçerli kullanıcıyı statik bir özellikten alan kod için kullanılabilir hale getirilir.
  * Geçerli kullanıcının `ClaimsPrincipal` kullanarak alın `HttpContextAccessor.HttpContext?.User` . Bu kod bir HTTP isteği bağlamı dışında kullanılırsa, `HttpContext` null olur.

`IHttpContextAccessor`Statik bir değişkende depolanan bir örneği kullanmanın son seçeneği, eklenen bağımlılıkların statik bağımlılıklara tercih edilmesi ASP.NET Core prensibi sahiptir. Bunun yerine, sonunda örnekleri alınacak şekilde planlayın `IHttpContextAccessor` . Statik bir yardımcı, kullanan büyük var olan ASP.NET uygulamalarını geçirirken yararlı bir köprü olabilir `ClaimsPrincipal.Current` .
