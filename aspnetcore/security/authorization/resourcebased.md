---
title: ASP.NET Core kaynak tabanlı yetkilendirme
author: scottaddie
description: Yetkilendirme özniteliği yeterli olmadığında kaynak tabanlı yetkilendirmeyi bir ASP.NET Core uygulamasına uygulamayı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: 202ca681a66ddf7f729d8835e2f77da846583df1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060176"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>ASP.NET Core kaynak tabanlı yetkilendirme

Yetkilendirme stratejisi erişilmekte olan kaynağa bağlıdır. Yazar özelliği olan bir belge düşünün. Yalnızca yazarın belgeyi güncelleştirmesine izin verilir. Sonuç olarak, yetkilendirme değerlendirmesi gerçekleşebilmesi için belgenin veri deposundan alınması gerekir.

Öznitelik değerlendirmesi, veri bağlamadan önce ve belgeyi yükleyen sayfa işleyicisinin veya eylemin yürütmeden önce oluşur. Bu nedenlerden dolayı, bir öznitelik ile bildirime dayalı yetkilendirme `[Authorize]` yeterli değildir. Bunun yerine, zorunlu &mdash; *Yetkilendirme* olarak bilinen bir stil için özel bir yetkilendirme yöntemi çağırabilirsiniz.

::: moniker range=">= aspnetcore-3.0"
[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([nasıl indirilir](xref:index#how-to-download-a-sample)).
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([nasıl indirilir](xref:index#how-to-download-a-sample)).
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([nasıl indirilir](xref:index#how-to-download-a-sample)).
::: moniker-end

[Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma](xref:security/authorization/secure-data) kaynak tabanlı yetkilendirme kullanan bir örnek uygulama içerir.

## <a name="use-imperative-authorization"></a>Kesinlik temelli yetkilendirme kullan

Yetkilendirme bir [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) hizmeti olarak uygulanır ve sınıfı içindeki hizmet koleksiyonuna kaydedilir `Startup` . Hizmet, sayfa işleyicilere veya eylemlere [bağımlılık ekleme](xref:fundamentals/dependency-injection) yoluyla kullanılabilir hale getirilir.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` iki `AuthorizeAsync` yöntem aşırı yüklemesi vardır: kaynağın ve ilke adının yanı sıra kaynağı kabul eden diğeri, değerlendirmek için gereksinimlerin bir listesi.

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

Aşağıdaki örnekte, güvenli hale getirilme kaynağı özel bir `Document` nesneye yüklenir. `AuthorizeAsync`Geçerli kullanıcının belirtilen belgeyi düzenlemesine izin verilip verilmeyeceğini belirlemekte bir aşırı yükleme çağrılır. Özel bir "EditPolicy" yetkilendirme ilkesi karara göre belirlenir. Yetkilendirme ilkeleri oluşturma hakkında daha fazla bilgi için bkz. [özel ilke tabanlı yetkilendirme](xref:security/authorization/policies) .

> [!NOTE]
> Aşağıdaki kod örnekleri, kimlik doğrulamasının çalıştırıldığını varsayar ve özelliğini ayarlar `User` .

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Kaynak tabanlı işleyici yazma

Kaynak tabanlı yetkilendirme için bir işleyici yazmak, [düz gereksinimler işleyicisi yazmaktan](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)çok farklı değildir. Özel bir gereksinim sınıfı oluşturun ve bir gereksinim işleyicisi sınıfı uygulayın. Gereksinim sınıfı oluşturma hakkında daha fazla bilgi için bkz. [Requirements](xref:security/authorization/policies#requirements).

Handler sınıfı hem gereksinim hem de kaynak türünü belirtir. Örneğin, bir ve kaynağı kullanan bir işleyici `SameAuthorRequirement` `Document` aşağıdaki gibidir:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

Yukarıdaki örnekte, `SameAuthorRequirement` daha genel bir sınıfta özel bir durum olduğunu düşünün `SpecificAuthorRequirement` . `SpecificAuthorRequirement`Sınıf (gösterilmez), `Name` yazarın adını temsil eden bir özelliği içerir. `Name`Özelliği geçerli kullanıcıya ayarlanabilir.

Gereksinimi ve işleyiciyi içine Kaydet `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>İşletimsel gereksinimler

CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerinin sonuçlarını temel alan kararlar verirken [Operationauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) yardımcı sınıfını kullanın. Bu sınıf her işlem türü için tek bir sınıf yerine tek bir işleyici yazmanızı sağlar. Kullanmak için, bazı işlem adlarını sağlayın:

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

İşleyici, bir `OperationAuthorizationRequirement` gereksinim ve bir kaynak kullanılarak aşağıdaki gibi uygulanır `Document` :

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

Önceki işleyici, kaynağı, kullanıcının kimliğini ve gereksinimin özelliğini kullanarak işlemi doğrular `Name` .

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>İşletimsel kaynak işleyicisiyle Challenge ve fordeklarasyon

Bu bölümde, Challenge ve fordeklarasyon eylem sonuçlarının nasıl işlendiği ve çekişme ve fordeklarasyonu 'nin nasıl farklı olduğu gösterilmektedir.

İşletimsel bir kaynak işleyicisini çağırmak için, `AuthorizeAsync` sayfa işleyicinizde veya eyleminde çağrılırken işlemi belirtin. Aşağıdaki örnek, kimliği doğrulanmış kullanıcının belirtilen belgeyi görüntülemesine izin verilip verilmeyeceğini belirler.

> [!NOTE]
> Aşağıdaki kod örnekleri, kimlik doğrulamasının çalıştırıldığını varsayar ve özelliğini ayarlar `User` .

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Yetkilendirme başarılı olursa belgeyi görüntüleme sayfası döndürülür. Yetkilendirme başarısız olursa, ancak kullanıcının kimliği doğrulanırsa, döndüren `ForbidResult` kimlik doğrulama ara yazılımı yetkilendirme başarısız olur. `ChallengeResult`Kimlik doğrulaması gerçekleştirilmesi gerektiğinde döndürülür. Etkileşimli tarayıcı istemcileri için, kullanıcıyı bir oturum açma sayfasına yönlendirmek uygun olabilir.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Yetkilendirme başarılı olursa belge görünümü döndürülür. Yetkilendirme başarısız olursa, geri `ChallengeResult` alma, yetkilendirme başarısız olan kimlik doğrulama ara yazılımını bildirir ve ara yazılım uygun yanıtı alabilir. Uygun bir yanıt 401 veya 403 durum kodu döndürüyor olabilir. Etkileşimli tarayıcı istemcileri için kullanıcıyı bir oturum açma sayfasına yeniden yönlendirmek anlamına gelebilir.

::: moniker-end
