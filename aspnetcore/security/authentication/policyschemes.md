---
title: ASP.NET Core 'de ilke şemaları
author: rick-anderson
description: Kimlik doğrulama ilkesi şemaları, tek bir mantıksal kimlik doğrulama şemasına sahip olmasını kolaylaştırır
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634988"
---
# <a name="policy-schemes-in-aspnet-core"></a>ASP.NET Core 'de ilke şemaları

Kimlik doğrulama ilkesi şemaları, tek bir mantıksal kimlik doğrulama düzeninin birden çok yaklaşım kullanmasına daha kolay olmasını sağlar. Örneğin, bir ilke şeması, sorunlar için Google kimlik doğrulaması ve cookie diğer her şey için kimlik doğrulaması kullanabilir. Kimlik doğrulama ilkesi şemaları şunları yapar:

* Herhangi bir kimlik doğrulama eylemini başka bir düzene iletmek kolay.
* İsteğe göre dinamik olarak ilet.

Türetilmiş <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> ve Ilişkili [authenticationhandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)kullanan tüm kimlik doğrulama şemaları:

* ASP.NET Core 2,1 ve sonraki sürümlerde otomatik olarak ilke düzenleridir.
* Düzenin seçenekleri yapılandırılarak etkinleştirilebilir.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Örnekler

Aşağıdaki örnek, alt düzey şemaları birleştiren daha yüksek düzey bir düzeni gösterir. Daha zorluk için Google kimlik doğrulaması kullanılır ve cookie diğer her şey için kimlik doğrulaması kullanılır:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Aşağıdaki örnek, istek temelinde dinamik düzen seçimini mümkün bir şekilde sunar. Diğer bir deyişle, cookie ve API kimlik doğrulamasını karıştırma:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
