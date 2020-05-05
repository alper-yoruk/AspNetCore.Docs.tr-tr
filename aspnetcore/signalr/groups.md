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
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="f509b-103">İçindeki kullanıcıları ve grupları yönetmeSignalR</span><span class="sxs-lookup"><span data-stu-id="f509b-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="f509b-104">[Brennan Conroy](https://github.com/BrennanConroy) tarafından</span><span class="sxs-lookup"><span data-stu-id="f509b-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="f509b-105">iletilerin, belirli bir kullanıcıyla ve adlandırılmış bağlantı gruplarının yanı sıra ilişkili tüm bağlantılara gönderilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f509b-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="f509b-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f509b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="f509b-107">İçindeki kullanıcılarSignalR</span><span class="sxs-lookup"><span data-stu-id="f509b-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="f509b-108">belirli bir kullanıcıyla ilişkili tüm bağlantılara ileti göndermenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f509b-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="f509b-109">Varsayılan olarak, SignalR Kullanıcı tanımlayıcısı `ClaimTypes.NameIdentifier` olarak bağlantıyla `ClaimsPrincipal` ilişkili öğesinden öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="f509b-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="f509b-110">Tek bir kullanıcının bir SignalR uygulamayla birden fazla bağlantısı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f509b-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="f509b-111">Örneğin, bir Kullanıcı masaüstüne ve telefonlarına bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f509b-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="f509b-112">Her cihazın ayrı SignalR bir bağlantısı vardır, ancak hepsi aynı kullanıcıyla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="f509b-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="f509b-113">Kullanıcıya bir ileti gönderildiğinde, bu kullanıcıyla ilişkili tüm bağlantılar iletiyi alır.</span><span class="sxs-lookup"><span data-stu-id="f509b-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="f509b-114">Bir bağlantı için Kullanıcı tanımlayıcısına, hub 'ınızdaki `Context.UserIdentifier` özellik tarafından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="f509b-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="f509b-115">Aşağıdaki örnekte gösterildiği gibi, kullanıcı tanımlayıcısını hub yönteminizin `User` işlevine geçirerek belirli bir kullanıcıya bir ileti gönderin:</span><span class="sxs-lookup"><span data-stu-id="f509b-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="f509b-116">Kullanıcı tanımlayıcısı büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="f509b-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="f509b-117">İçindeki gruplarSignalR</span><span class="sxs-lookup"><span data-stu-id="f509b-117">Groups in SignalR</span></span>

<span data-ttu-id="f509b-118">Grup, bir adla ilişkili bir bağlantı koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="f509b-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="f509b-119">İletiler, bir gruptaki tüm bağlantılara gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="f509b-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="f509b-120">Gruplar uygulama tarafından yönetildiğinden, gruplar bir bağlantıya veya birden çok bağlantıya göndermek için önerilen yoldur.</span><span class="sxs-lookup"><span data-stu-id="f509b-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="f509b-121">Bir bağlantı, birden fazla grubun üyesi olabilir.</span><span class="sxs-lookup"><span data-stu-id="f509b-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="f509b-122">Bu, grupları her odanın bir grup olarak gösterilebileceği bir sohbet uygulaması gibi bir şey için ideal hale getirir.</span><span class="sxs-lookup"><span data-stu-id="f509b-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="f509b-123">`AddToGroupAsync` Ve `RemoveFromGroupAsync` yöntemleri aracılığıyla bir gruba bağlantılar eklenebilir veya gruplardan kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f509b-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="f509b-124">Bağlantı yeniden bağlandığında grup üyeliği korunmaz.</span><span class="sxs-lookup"><span data-stu-id="f509b-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="f509b-125">Bağlantı, yeniden oluşturulduğunda gruba yeniden katılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f509b-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="f509b-126">Uygulama birden çok sunucuya ölçeklendirildiyse bu bilgiler kullanılamadığından, bir grubun üyelerini saymak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="f509b-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="f509b-127">Grupları kullanırken kaynaklara erişimi korumak için ASP.NET Core [kimlik doğrulaması ve yetkilendirme](xref:signalr/authn-and-authz) işlevini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f509b-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="f509b-128">Yalnızca bu grup için kimlik bilgileri geçerliyse bir gruba Kullanıcı eklerseniz, bu gruba gönderilen iletiler yalnızca yetkili kullanıcılara gider.</span><span class="sxs-lookup"><span data-stu-id="f509b-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="f509b-129">Ancak, gruplar bir güvenlik özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="f509b-129">However, groups are not a security feature.</span></span> <span data-ttu-id="f509b-130">Kimlik doğrulama talepleri, süre sonu ve iptal gibi grupların olmadığı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f509b-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="f509b-131">Bir kullanıcının gruba erişim izni iptal edildiğinde, bunu el ile tespit etmeniz ve gruptan kaldırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="f509b-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="f509b-132">Grup adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="f509b-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="f509b-133">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f509b-133">Related resources</span></span>

* [<span data-ttu-id="f509b-134">başlarken</span><span class="sxs-lookup"><span data-stu-id="f509b-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f509b-135">Merkezler</span><span class="sxs-lookup"><span data-stu-id="f509b-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f509b-136">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="f509b-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
