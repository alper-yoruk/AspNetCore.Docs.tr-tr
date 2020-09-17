---
title: ASP.NET Core MVC için uyumluluk sürümü
author: rick-anderson
description: ASP.NET Core başlangıç sınıfının Hizmetleri ve uygulamanın istek ardışık düzenini nasıl yapılandırdığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
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
uid: mvc/compatibility-version
ms.openlocfilehash: b7d14aa0371fcc01d568cf1e7b9908c4a8d984df
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722838"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>ASP.NET Core MVC için uyumluluk sürümü

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Yöntemi, ASP.NET Core 3,0 uygulamaları için bir op değildir. Diğer bir deyişle, `SetCompatibilityVersion` herhangi bir değeriyle çağırmak <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> uygulama üzerinde hiçbir etkiye sahip değildir.

* ASP.NET Core sonraki küçük sürümü yeni bir `CompatibilityVersion` değer sağlayabilir.
* `CompatibilityVersion``Version_2_0`üzerinden değerler `Version_2_2` işaretlenir `[Obsolete(...)]` .
* Bkz. [Antiforgery, CORS, Diagnostics, MVC ve yönlendirme IÇINDEKI API değişikliklerini bozma](https://github.com/aspnet/Announcements/issues/387). Bu liste, uyumluluk anahtarlarına yönelik son değişiklikleri içerir.

`SetCompatibilityVersion`ASP.NET Core 2. x uygulamalarıyla nasıl çalıştığını görmek için, [bu makalenin ASP.NET Core 2,2 sürümünü](?view=aspnetcore-2.2)seçin.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Yöntemi, ASP.NET Core 2. x uygulamasının, ASP.NET Core MVC 2,1 veya 2,2 ' de ortaya çıkan olası davranış değişikliklerinin kabul veya devre dışı olmasına olanak sağlar. Bu olası davranışı ortadan kaldırma, MVC alt sisteminin davranışını ve **kodunuzun** çalışma zamanı tarafından nasıl çağrıldığını gösterir. ' De, en son davranışı ve ASP.NET Core uzun vadeli davranışını alırsınız.

Aşağıdaki kod uyumluluk modunu ASP.NET Core 2,2 olarak ayarlar:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

Uygulamanızı en son sürümü () kullanarak test etmenizi öneririz `CompatibilityVersion.Latest` . Çoğu uygulamanın en son sürümü kullanarak önemli davranış değişikliklerinin olmadığı tahmin ederiz.

Çağıran uygulamalar `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` , ASP.NET Core 2.1/2.2 MVC sürümlerinde tanıtılan büyük olasılıkla davranış değişikliklerinden korunmaktadır. Bu koruma:

* Tüm 2,1 ve sonraki değişikliklere uygulanmaz, MVC alt sisteminde çalışma zamanı davranış değişikliklerinin ASP.NET Core büyük olasılıkla bozmasına yöneliktir.
* ASP.NET Core 3,0 ' ye genişlemez.

ASP.NET Core 2,1 ve 2,2 **olmayan** uygulamalar için varsayılan uyumluluk, `SetCompatibilityVersion` 2,0 uyumluluğuna yöneliktir. Diğer bir deyişle, çağırma `SetCompatibilityVersion` ile aynı değildir `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` .

Aşağıdaki kod, uyumluluk modunu aşağıdaki davranışlar dışında 2,2 ASP.NET Core olarak ayarlar:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

Doğru uyumluluk anahtarlarını kullanarak, bölme davranışı ile ilgili olan uygulamalar için:

* En son sürümü kullanmanıza ve belirli bir bölme davranışı değişikliğini geri yüklemenize olanak tanır.
* , Uygulamanızı en son değişikliklerle çalışacak şekilde güncelleştirmeniz için size zaman kazandırır.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions>Belgelerde nelerin değiştiğini ve değişikliklerin çoğu kullanıcı için nasıl gelişdiğinin iyi bir açıklaması vardır.

ASP.NET Core 3,0 ile, uyumluluk anahtarları tarafından desteklenen eski davranışlar kaldırılmıştır. Bunlar, neredeyse tüm kullanıcılar faydalanmasını olumlu değişiklikler olduğunu hissettik. Bu değişiklikleri 2,1 ve 2,2 ' de sunarak, çoğu uygulama yararlı olabilir, diğerleri ise güncelleştirilmeye zaman alabilir.
::: moniker-end