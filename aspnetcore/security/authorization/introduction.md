---
title: ASP.NET Core yetkilendirme için giriş
author: rick-anderson
description: ASP.NET Core uygulamalarda yetkilendirme ve yetkilendirme ile ilgili temel bilgileri öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/introduction
ms.openlocfilehash: 7d7570ead1365588fd582d9bea364685da29a576
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634442"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core yetkilendirme için giriş

<a name="security-authorization-introduction"></a>

Yetkilendirme, bir kullanıcının ne yapabileceğini belirleyen işlemi ifade eder. Örneğin, bir yönetici kullanıcının belge kitaplığı oluşturmalarına, belge eklemesine, belge düzenlemesine ve bunları silmesine izin verilir. Kitaplıkla çalışan yönetici olmayan bir Kullanıcı yalnızca belgeleri okuma yetkisine sahiptir.

Yetkilendirme, kimlik doğrulamasından dik ve bağımsızdır. Ancak, yetkilendirme bir kimlik doğrulama mekanizması gerektirir. Kimlik doğrulaması, kullanıcı kim olduğunu belirlememe işlemidir. Kimlik doğrulaması geçerli kullanıcı için bir veya daha fazla kimlik oluşturabilir.

ASP.NET Core kimlik doğrulaması hakkında daha fazla bilgi için bkz <xref:security/authentication/index> ..

## <a name="authorization-types"></a>Yetkilendirme türleri

ASP.NET Core yetkilendirme basit, bildirime dayalı bir [rol](xref:security/authorization/roles) ve zengin bir [ilke tabanlı](xref:security/authorization/policies) model sağlar. Yetkilendirme, gereksinimlerde ifade edilir ve işleyiciler bir kullanıcının gereksinimlerine göre taleplerini değerlendirir. Zorunlu denetimler, kullanıcının erişmeye çalıştığı kaynağın Kullanıcı kimliğini ve özelliklerini değerlendiren basit ilkeleri veya ilkeleri temel alabilir.

## <a name="namespaces"></a>Ad alanları

Ve özniteliklerini de içeren yetkilendirme `AuthorizeAttribute` bileşenleri `AllowAnonymousAttribute` `Microsoft.AspNetCore.Authorization` ad alanında bulunur.

[Basit yetkilendirme](xref:security/authorization/simple)ile ilgili belgelere başvurun.
