---
title: İçindeki kullanıcıları ve grupları yönetmeSignalR
author: bradygaster
description: ASP.NET Core SignalR Kullanıcı ve grup yönetimine genel bakış.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776304"
---
# <a name="manage-users-and-groups-in-signalr"></a>İçindeki kullanıcıları ve grupları yönetmeSignalR

[Brennan Conroy](https://github.com/BrennanConroy) tarafından

SignalRiletilerin, belirli bir kullanıcıyla ve adlandırılmış bağlantı gruplarının yanı sıra ilişkili tüm bağlantılara gönderilmesine izin verir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>İçindeki kullanıcılarSignalR

SignalRbelirli bir kullanıcıyla ilişkili tüm bağlantılara ileti göndermenizi sağlar. Varsayılan olarak, SignalR Kullanıcı tanımlayıcısı `ClaimTypes.NameIdentifier` olarak bağlantıyla `ClaimsPrincipal` ilişkili öğesinden öğesini kullanır. Tek bir kullanıcının bir SignalR uygulamayla birden fazla bağlantısı olabilir. Örneğin, bir Kullanıcı masaüstüne ve telefonlarına bağlanabilir. Her cihazın ayrı SignalR bir bağlantısı vardır, ancak hepsi aynı kullanıcıyla ilişkilendirilir. Kullanıcıya bir ileti gönderildiğinde, bu kullanıcıyla ilişkili tüm bağlantılar iletiyi alır. Bir bağlantı için Kullanıcı tanımlayıcısına, hub 'ınızdaki `Context.UserIdentifier` özellik tarafından erişilebilir.

Aşağıdaki örnekte gösterildiği gibi, kullanıcı tanımlayıcısını hub yönteminizin `User` işlevine geçirerek belirli bir kullanıcıya bir ileti gönderin:

> [!NOTE]
> Kullanıcı tanımlayıcısı büyük/küçük harfe duyarlıdır.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>İçindeki gruplarSignalR

Grup, bir adla ilişkili bir bağlantı koleksiyonudur. İletiler, bir gruptaki tüm bağlantılara gönderilebilir. Gruplar uygulama tarafından yönetildiğinden, gruplar bir bağlantıya veya birden çok bağlantıya göndermek için önerilen yoldur. Bir bağlantı, birden fazla grubun üyesi olabilir. Bu, grupları her odanın bir grup olarak gösterilebileceği bir sohbet uygulaması gibi bir şey için ideal hale getirir. `AddToGroupAsync` Ve `RemoveFromGroupAsync` yöntemleri aracılığıyla bir gruba bağlantılar eklenebilir veya gruplardan kaldırılabilir.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

Bağlantı yeniden bağlandığında grup üyeliği korunmaz. Bağlantı, yeniden oluşturulduğunda gruba yeniden katılması gerekir. Uygulama birden çok sunucuya ölçeklendirildiyse bu bilgiler kullanılamadığından, bir grubun üyelerini saymak mümkün değildir.

Grupları kullanırken kaynaklara erişimi korumak için ASP.NET Core [kimlik doğrulaması ve yetkilendirme](xref:signalr/authn-and-authz) işlevini kullanın. Yalnızca bu grup için kimlik bilgileri geçerliyse bir gruba Kullanıcı eklerseniz, bu gruba gönderilen iletiler yalnızca yetkili kullanıcılara gider. Ancak, gruplar bir güvenlik özelliği değildir. Kimlik doğrulama talepleri, süre sonu ve iptal gibi grupların olmadığı özelliklere sahiptir. Bir kullanıcının gruba erişim izni iptal edildiğinde, bunu el ile tespit etmeniz ve gruptan kaldırmanız gerekir.

> [!NOTE]
> Grup adları büyük/küçük harfe duyarlıdır.

## <a name="related-resources"></a>İlgili kaynaklar

* [başlarken](xref:tutorials/signalr)
* [Merkezler](xref:signalr/hubs)
* [Azure’da Yayımlama](xref:signalr/publish-to-azure-web-app)
