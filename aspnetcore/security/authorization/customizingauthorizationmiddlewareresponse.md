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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="37b9a-103">Authorizationara yazılım davranışını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="37b9a-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="37b9a-104">Uygulamalar `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` , ara yazılım yetkilendirme sonuçlarını işleme biçimini özelleştirmek için bir kayıt yapabilir.</span><span class="sxs-lookup"><span data-stu-id="37b9a-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="37b9a-105">Uygulamalar şu şekilde özelleştirilmiş ara yazılımı kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="37b9a-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="37b9a-106">Özelleştirilmiş yanıtları döndürün.</span><span class="sxs-lookup"><span data-stu-id="37b9a-106">Return customized responses.</span></span>
* <span data-ttu-id="37b9a-107">Varsayılan zorluk veya fordeklarasyon yanıtlarını geliştirin.</span><span class="sxs-lookup"><span data-stu-id="37b9a-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="37b9a-108">Aşağıdaki kod, belirli kimlik doğrulama hatalarının özel bir yanıtını döndüren bir yetkilendirme işleyicisi örneğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="37b9a-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="37b9a-109">Kaydolma `MyAuthorizationMiddlewareResultHandler` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="37b9a-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->