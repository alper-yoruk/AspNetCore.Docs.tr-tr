---
title: ASP.NET Core kaynak tabanlı yetkilendirme
author: scottaddie
description: Yetkilendirme özniteliği yeterli olmadığında kaynak tabanlı yetkilendirmeyi bir ASP.NET Core uygulamasına uygulamayı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: ee9b3e9b0085d58778fdf0c0f9a5d352747d88ba
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020138"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="800e1-103">ASP.NET Core kaynak tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="800e1-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="800e1-104">Yetkilendirme stratejisi erişilmekte olan kaynağa bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="800e1-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="800e1-105">Yazar özelliği olan bir belge düşünün.</span><span class="sxs-lookup"><span data-stu-id="800e1-105">Consider a document that has an author property.</span></span> <span data-ttu-id="800e1-106">Yalnızca yazarın belgeyi güncelleştirmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="800e1-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="800e1-107">Sonuç olarak, yetkilendirme değerlendirmesi gerçekleşebilmesi için belgenin veri deposundan alınması gerekir.</span><span class="sxs-lookup"><span data-stu-id="800e1-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="800e1-108">Öznitelik değerlendirmesi, veri bağlamadan önce ve belgeyi yükleyen sayfa işleyicisinin veya eylemin yürütmeden önce oluşur.</span><span class="sxs-lookup"><span data-stu-id="800e1-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="800e1-109">Bu nedenlerden dolayı, bir öznitelik ile bildirime dayalı yetkilendirme `[Authorize]` yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="800e1-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="800e1-110">Bunun yerine, zorunlu &mdash; *Yetkilendirme*olarak bilinen bir stil için özel bir yetkilendirme yöntemi çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="800e1-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="800e1-111">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="800e1-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="800e1-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="800e1-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="800e1-113">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="800e1-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="800e1-114">[Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma](xref:security/authorization/secure-data) kaynak tabanlı yetkilendirme kullanan bir örnek uygulama içerir.</span><span class="sxs-lookup"><span data-stu-id="800e1-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="800e1-115">Kesinlik temelli yetkilendirme kullan</span><span class="sxs-lookup"><span data-stu-id="800e1-115">Use imperative authorization</span></span>

<span data-ttu-id="800e1-116">Yetkilendirme bir [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) hizmeti olarak uygulanır ve sınıfı içindeki hizmet koleksiyonuna kaydedilir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="800e1-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="800e1-117">Hizmet, sayfa işleyicilere veya eylemlere [bağımlılık ekleme](xref:fundamentals/dependency-injection) yoluyla kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="800e1-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="800e1-118">`IAuthorizationService`iki `AuthorizeAsync` yöntem aşırı yüklemesi vardır: kaynağın ve ilke adının yanı sıra kaynağı kabul eden diğeri, değerlendirmek için gereksinimlerin bir listesi.</span><span class="sxs-lookup"><span data-stu-id="800e1-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="800e1-119">Aşağıdaki örnekte, güvenli hale getirilme kaynağı özel bir `Document` nesneye yüklenir.</span><span class="sxs-lookup"><span data-stu-id="800e1-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="800e1-120">`AuthorizeAsync`Geçerli kullanıcının belirtilen belgeyi düzenlemesine izin verilip verilmeyeceğini belirlemekte bir aşırı yükleme çağrılır.</span><span class="sxs-lookup"><span data-stu-id="800e1-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="800e1-121">Özel bir "EditPolicy" yetkilendirme ilkesi karara göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="800e1-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="800e1-122">Yetkilendirme ilkeleri oluşturma hakkında daha fazla bilgi için bkz. [özel ilke tabanlı yetkilendirme](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="800e1-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="800e1-123">Aşağıdaki kod örnekleri, kimlik doğrulamasının çalıştırıldığını varsayar ve özelliğini ayarlar `User` .</span><span class="sxs-lookup"><span data-stu-id="800e1-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="800e1-124">Kaynak tabanlı işleyici yazma</span><span class="sxs-lookup"><span data-stu-id="800e1-124">Write a resource-based handler</span></span>

<span data-ttu-id="800e1-125">Kaynak tabanlı yetkilendirme için bir işleyici yazmak, [düz gereksinimler işleyicisi yazmaktan](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)çok farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="800e1-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="800e1-126">Özel bir gereksinim sınıfı oluşturun ve bir gereksinim işleyicisi sınıfı uygulayın.</span><span class="sxs-lookup"><span data-stu-id="800e1-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="800e1-127">Gereksinim sınıfı oluşturma hakkında daha fazla bilgi için bkz. [Requirements](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="800e1-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="800e1-128">Handler sınıfı hem gereksinim hem de kaynak türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="800e1-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="800e1-129">Örneğin, bir ve kaynağı kullanan bir işleyici `SameAuthorRequirement` `Document` aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="800e1-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="800e1-130">Yukarıdaki örnekte, `SameAuthorRequirement` daha genel bir sınıfta özel bir durum olduğunu düşünün `SpecificAuthorRequirement` .</span><span class="sxs-lookup"><span data-stu-id="800e1-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="800e1-131">`SpecificAuthorRequirement`Sınıf (gösterilmez), `Name` yazarın adını temsil eden bir özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="800e1-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="800e1-132">`Name`Özelliği geçerli kullanıcıya ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="800e1-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="800e1-133">Gereksinimi ve işleyiciyi içine Kaydet `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="800e1-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="800e1-134">İşletimsel gereksinimler</span><span class="sxs-lookup"><span data-stu-id="800e1-134">Operational requirements</span></span>

<span data-ttu-id="800e1-135">CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerinin sonuçlarını temel alan kararlar verirken [Operationauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) yardımcı sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="800e1-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="800e1-136">Bu sınıf her işlem türü için tek bir sınıf yerine tek bir işleyici yazmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="800e1-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="800e1-137">Kullanmak için, bazı işlem adlarını sağlayın:</span><span class="sxs-lookup"><span data-stu-id="800e1-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="800e1-138">İşleyici, bir `OperationAuthorizationRequirement` gereksinim ve bir kaynak kullanılarak aşağıdaki gibi uygulanır `Document` :</span><span class="sxs-lookup"><span data-stu-id="800e1-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="800e1-139">Önceki işleyici, kaynağı, kullanıcının kimliğini ve gereksinimin özelliğini kullanarak işlemi doğrular `Name` .</span><span class="sxs-lookup"><span data-stu-id="800e1-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="800e1-140">İşletimsel kaynak işleyicisiyle Challenge ve fordeklarasyon</span><span class="sxs-lookup"><span data-stu-id="800e1-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="800e1-141">Bu bölümde, Challenge ve fordeklarasyon eylem sonuçlarının nasıl işlendiği ve çekişme ve fordeklarasyonu 'nin nasıl farklı olduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="800e1-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="800e1-142">İşletimsel bir kaynak işleyicisini çağırmak için, `AuthorizeAsync` sayfa işleyicinizde veya eyleminde çağrılırken işlemi belirtin.</span><span class="sxs-lookup"><span data-stu-id="800e1-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="800e1-143">Aşağıdaki örnek, kimliği doğrulanmış kullanıcının belirtilen belgeyi görüntülemesine izin verilip verilmeyeceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="800e1-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="800e1-144">Aşağıdaki kod örnekleri, kimlik doğrulamasının çalıştırıldığını varsayar ve özelliğini ayarlar `User` .</span><span class="sxs-lookup"><span data-stu-id="800e1-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="800e1-145">Yetkilendirme başarılı olursa belgeyi görüntüleme sayfası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="800e1-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="800e1-146">Yetkilendirme başarısız olursa, ancak kullanıcının kimliği doğrulanırsa, döndüren `ForbidResult` kimlik doğrulama ara yazılımı yetkilendirme başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="800e1-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="800e1-147">`ChallengeResult`Kimlik doğrulaması gerçekleştirilmesi gerektiğinde döndürülür.</span><span class="sxs-lookup"><span data-stu-id="800e1-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="800e1-148">Etkileşimli tarayıcı istemcileri için, kullanıcıyı bir oturum açma sayfasına yönlendirmek uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="800e1-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="800e1-149">Yetkilendirme başarılı olursa belge görünümü döndürülür.</span><span class="sxs-lookup"><span data-stu-id="800e1-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="800e1-150">Yetkilendirme başarısız olursa, geri `ChallengeResult` alma, yetkilendirme başarısız olan kimlik doğrulama ara yazılımını bildirir ve ara yazılım uygun yanıtı alabilir.</span><span class="sxs-lookup"><span data-stu-id="800e1-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="800e1-151">Uygun bir yanıt 401 veya 403 durum kodu döndürüyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="800e1-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="800e1-152">Etkileşimli tarayıcı istemcileri için kullanıcıyı bir oturum açma sayfasına yeniden yönlendirmek anlamına gelebilir.</span><span class="sxs-lookup"><span data-stu-id="800e1-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
