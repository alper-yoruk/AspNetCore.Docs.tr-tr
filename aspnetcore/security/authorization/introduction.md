---
title: ASP.NET Core yetkilendirme için giriş
author: rick-anderson
description: ASP.NET Core uygulamalarda yetkilendirme ve yetkilendirme ile ilgili temel bilgileri öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
ms.openlocfilehash: 215c61b034abf530010b7beeb58100a1ff0e8eb3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022127"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="ee799-103">ASP.NET Core yetkilendirme için giriş</span><span class="sxs-lookup"><span data-stu-id="ee799-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="ee799-104">Yetkilendirme, bir kullanıcının ne yapabileceğini belirleyen işlemi ifade eder.</span><span class="sxs-lookup"><span data-stu-id="ee799-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="ee799-105">Örneğin, bir yönetici kullanıcının belge kitaplığı oluşturmalarına, belge eklemesine, belge düzenlemesine ve bunları silmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="ee799-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="ee799-106">Kitaplıkla çalışan yönetici olmayan bir Kullanıcı yalnızca belgeleri okuma yetkisine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ee799-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="ee799-107">Yetkilendirme, kimlik doğrulamasından dik ve bağımsızdır.</span><span class="sxs-lookup"><span data-stu-id="ee799-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="ee799-108">Ancak, yetkilendirme bir kimlik doğrulama mekanizması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ee799-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="ee799-109">Kimlik doğrulaması, kullanıcı kim olduğunu belirlememe işlemidir.</span><span class="sxs-lookup"><span data-stu-id="ee799-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="ee799-110">Kimlik doğrulaması geçerli kullanıcı için bir veya daha fazla kimlik oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="ee799-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="ee799-111">ASP.NET Core kimlik doğrulaması hakkında daha fazla bilgi için bkz <xref:security/authentication/index> ..</span><span class="sxs-lookup"><span data-stu-id="ee799-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="ee799-112">Yetkilendirme türleri</span><span class="sxs-lookup"><span data-stu-id="ee799-112">Authorization types</span></span>

<span data-ttu-id="ee799-113">ASP.NET Core yetkilendirme basit, bildirime dayalı bir [rol](xref:security/authorization/roles) ve zengin bir [ilke tabanlı](xref:security/authorization/policies) model sağlar.</span><span class="sxs-lookup"><span data-stu-id="ee799-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="ee799-114">Yetkilendirme, gereksinimlerde ifade edilir ve işleyiciler bir kullanıcının gereksinimlerine göre taleplerini değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="ee799-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="ee799-115">Zorunlu denetimler, kullanıcının erişmeye çalıştığı kaynağın Kullanıcı kimliğini ve özelliklerini değerlendiren basit ilkeleri veya ilkeleri temel alabilir.</span><span class="sxs-lookup"><span data-stu-id="ee799-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="ee799-116">Ad Alanları</span><span class="sxs-lookup"><span data-stu-id="ee799-116">Namespaces</span></span>

<span data-ttu-id="ee799-117">Ve özniteliklerini de içeren yetkilendirme `AuthorizeAttribute` bileşenleri `AllowAnonymousAttribute` `Microsoft.AspNetCore.Authorization` ad alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="ee799-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="ee799-118">[Basit yetkilendirme](xref:security/authorization/simple)ile ilgili belgelere başvurun.</span><span class="sxs-lookup"><span data-stu-id="ee799-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
