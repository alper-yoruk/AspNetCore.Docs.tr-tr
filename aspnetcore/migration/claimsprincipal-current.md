---
title: ClaimsPrincipal. Current öğesinden geçir
author: mjrousos
description: Geçerli kimliği doğrulanmış kullanıcının kimliğini ve ASP.NET Core taleplerini almak için ClaimsPrincipal. Current ' dan uzağa geçiş yapmayı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776096"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>ClaimsPrincipal. Current öğesinden geçir

ASP.NET 4. x projelerinde, geçerli kimliği doğrulanmış kullanıcının kimliğini ve taleplerini almak için [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) kullanımı yaygındır. ASP.NET Core, bu özellik artık ayarlı değildir. Kendisine bağlı olan kodun, geçerli kimliği doğrulanmış kullanıcının kimliğini farklı yollarla alması için güncelleştirilmesi gerekir.

## <a name="context-specific-data-instead-of-static-data"></a>Statik veriler yerine içeriğe özgü veriler

ASP.NET Core kullanılırken, her ikisi de `ClaimsPrincipal.Current` değeri ayarlı `Thread.CurrentPrincipal` değildir. Bu özelliklerin her ikisi de ASP.NET Core genellikle kaçınan statik durumu temsil eder. Bunun yerine, ASP.NET Core mimarisi, bağlama özgü hizmet koleksiyonlarından (geçerli kullanıcının kimliği gibi) bağımlılıkları ( [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) modelini kullanarak) almak. Daha fazlası, `Thread.CurrentPrincipal` iş parçacığı statiktir, bu nedenle bazı zaman uyumsuz senaryolarda değişiklikleri kalıcı hale getiremeyebilir ( `ClaimsPrincipal.Current` ve yalnızca `Thread.CurrentPrincipal` varsayılan olarak çağrı yapılır).

Statik üyelerin zaman uyumsuz senaryolarda neden olabileceği sorun sayısını anlamak için aşağıdaki kod parçacığını göz önünde bulundurun:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

Önceki örnek kod, zaman `Thread.CurrentPrincipal` uyumsuz bir çağrıyı beklerken önce ve sonra değerini ayarlar ve denetler. `Thread.CurrentPrincipal`, ayarlandığı *iş parçacığına* özeldir ve yöntemi await sonrasında farklı bir iş parçacığında yürütmeyi sürdürülemez. Sonuç olarak `Thread.CurrentPrincipal` , ilk kez denetlendiğinde ancak çağrısından sonra null olduğunda vardır `await Task.Yield()`.

Test kimlikleri kolay bir şekilde eklenebilir olduğundan, geçerli kullanıcının kimliğini uygulamanın dı hizmeti koleksiyonundan almak daha kararlı değildir.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>ASP.NET Core uygulamasındaki geçerli kullanıcıyı alma

Şu yerine, kimliği doğrulanmış geçerli kullanıcının `ClaimsPrincipal` ASP.NET Core alınması için birkaç seçenek vardır: `ClaimsPrincipal.Current`

* **ControllerBase. User**. MVC denetleyicileri, geçerli kimliği doğrulanmış kullanıcıya [Kullanıcı](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) özelliği ile erişebilir.
* **HttpContext. User**. Geçerli `HttpContext` kullanıcıya erişimi olan bileşenler (örneğin, ara yazılım), geçerli kullanıcıyı `ClaimsPrincipal` [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)öğesinden alabilir.
* **Çağırandan geçirildi**. Güncel `HttpContext` erişimi olmayan kitaplıklar, genellikle denetleyiciler veya ara yazılım bileşenlerinden çağırılır ve geçerli kullanıcının kimliği bir bağımsız değişken olarak geçirilebilir.
* **Ihttpcontextaccessor**. ASP.NET Core geçirilecek proje, geçerli kullanıcının kimliğini tüm gerekli konumlara kolayca geçiremeyecek kadar büyük olabilir. Bu gibi durumlarda, [ıhttpcontextaccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) geçici çözüm olarak kullanılabilir. `IHttpContextAccessor`geçerli `HttpContext` (varsa) erişebilir. Eğer dı kullanılıyorsa, bkz <xref:fundamentals/httpcontext>.. Geçerli kullanıcının kimliğini, ASP.NET Core dı odaklı mimarisiyle çalışacak şekilde güncelleştirilmemiş kodda almaya yönelik kısa süreli bir çözüm şu şekilde olacaktır:

  * ' `IHttpContextAccessor` De `Startup.ConfigureServices` [ADDHTTPCONTEXTACCESSOR](https://github.com/aspnet/Hosting/issues/793) 'ı çağırarak dı kapsayıcısında kullanılabilir hale getirin.
  * Başlatma `IHttpContextAccessor` sırasında bir örneğini alın ve statik bir değişkende depolayın. Örnek, daha önce geçerli kullanıcıyı statik bir özellikten alan kod için kullanılabilir hale getirilir.
  * Geçerli kullanıcının `ClaimsPrincipal` kullanarak `HttpContextAccessor.HttpContext?.User`alın. Bu kod bir HTTP isteği bağlamı dışında kullanılırsa, null `HttpContext` olur.

Statik bir değişkende depolanan bir `IHttpContextAccessor` örneğin kullanıldığı son seçenek, ASP.NET Core prensicüler (statik bağımlılıklara eklenen bağımlılıklar tercih edilir). Bunun yerine bağımlılık ekleme `IHttpContextAccessor` işleminden en sonunda örnekleri almayı planlayın. Statik bir yardımcı, daha önce kullanan `ClaimsPrincipal.Current`büyük var olan ASP.NET uygulamalarını geçirirken yararlı bir köprü olabilir.
