---
title: Authorizationara yazılım davranışını özelleştirme
author: pranavkm
ms.author: prkrishn
description: Bu makalede, Authorizationbir ara yazılım için sonuç işlemenin nasıl özelleştirileceği açıklanır.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156786"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Authorizationara yazılım davranışını özelleştirme

Uygulamalar `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` , ara yazılım yetkilendirme sonuçlarını işleme biçimini özelleştirmek için bir kayıt yapabilir. Uygulamalar şu şekilde özelleştirilmiş ara yazılımı kullanabilir:

* Özelleştirilmiş yanıtları döndürün.
* Varsayılan zorluk veya fordeklarasyon yanıtlarını geliştirin.

Aşağıdaki kod, belirli kimlik doğrulama hatalarının özel bir yanıtını döndüren bir yetkilendirme işleyicisi örneğini gösterir:

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

Kaydolma `MyAuthorizationMiddlewareResultHandler` `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->