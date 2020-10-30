---
title: İçindeki kullanıcıları ve grupları yönetme SignalR
author: bradygaster
description: ASP.NET Core SignalR Kullanıcı ve grup yönetimine genel bakış.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: a86408eaae8d3df32faef79453d9db0cdbd64a78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050959"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a>İçindeki kullanıcıları ve grupları yönetme SignalR

[Brennan Conroy](https://github.com/BrennanConroy) tarafından

SignalR iletilerin, belirli bir kullanıcıyla ve adlandırılmış bağlantı gruplarının yanı sıra ilişkili tüm bağlantılara gönderilmesine izin verir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="users-in-no-locsignalr"></a>İçindeki kullanıcılar SignalR

Uygulamasındaki tek bir kullanıcının SignalR bir uygulamayla birden çok bağlantısı olabilir. Örneğin, bir Kullanıcı masaüstüne ve telefonlarına bağlanabilir. Her cihazın ayrı bir SignalR bağlantısı vardır, ancak hepsi aynı kullanıcıyla ilişkilendirilir. Kullanıcıya bir ileti gönderildiğinde, bu kullanıcıyla ilişkili tüm bağlantılar iletiyi alır. Bir bağlantı için Kullanıcı tanımlayıcısına, `Context.UserIdentifier` hub 'daki özellik tarafından erişilebilir.

Varsayılan olarak, SignalR `ClaimTypes.NameIdentifier` `ClaimsPrincipal` Kullanıcı tanımlayıcısı olarak bağlantıyla ilişkili öğesinden öğesini kullanır. Bu davranışı özelleştirmek için bkz. [kimlik işlemeyi özelleştirmek için talepleri kullanma](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

`User`Aşağıdaki örnekte gösterildiği gibi, kullanıcı tanımlayıcısını bir hub yöntemindeki işleve geçirerek belirli bir kullanıcıya bir ileti gönderin:

> [!NOTE]
> Kullanıcı tanımlayıcısı büyük/küçük harfe duyarlıdır.

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a>İçindeki gruplar SignalR

Grup, bir adla ilişkili bir bağlantı koleksiyonudur. İletiler, bir gruptaki tüm bağlantılara gönderilebilir. Gruplar uygulama tarafından yönetildiğinden, gruplar bir bağlantıya veya birden çok bağlantıya göndermek için önerilen yoldur. Bir bağlantı, birden fazla grubun üyesi olabilir. Gruplar, her odanın bir grup olarak gösterilebileceği bir sohbet uygulaması gibi bir şey için idealdir. Ve yöntemleri aracılığıyla gruplara eklenen veya gruplardan bağlantılar kaldırılır `AddToGroupAsync` `RemoveFromGroupAsync` .

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

Bağlantı yeniden bağlandığında grup üyeliği korunmaz. Bağlantı, yeniden oluşturulduğunda gruba yeniden katılması gerekir. Uygulama birden çok sunucuya ölçeklendirildiyse bu bilgiler kullanılamadığından, bir grubun üyelerini saymak mümkün değildir.

Grupları kullanırken kaynaklara erişimi korumak için ASP.NET Core [kimlik doğrulaması ve yetkilendirme](xref:signalr/authn-and-authz) işlevini kullanın. Bir Kullanıcı gruba yalnızca kimlik bilgileri bu grup için geçerli olduğunda eklenirse, bu gruba gönderilen iletiler yalnızca yetkili kullanıcılara gider. Ancak, gruplar bir güvenlik özelliği değildir. Kimlik doğrulama talepleri, süre sonu ve iptal gibi grupların olmadığı özelliklere sahiptir. Kullanıcının gruba erişim izni iptal edildiğinde, uygulamanın kullanıcıyı gruptan açıkça kaldırması gerekir.

> [!NOTE]
> Grup adları büyük/küçük harfe duyarlıdır.

## <a name="related-resources"></a>İlgili kaynaklar

* [Kullanmaya başlama](xref:tutorials/signalr)
* [Merkezler](xref:signalr/hubs)
* [Azure 'da yayımlama](xref:signalr/publish-to-azure-web-app)
