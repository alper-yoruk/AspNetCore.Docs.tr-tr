---
title: 'İçindeki kullanıcıları ve grupları yönetme :::no-loc(SignalR):::'
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: Kullanıcı ve grup yönetimine genel bakış.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/groups
ms.openlocfilehash: a86408eaae8d3df32faef79453d9db0cdbd64a78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050959"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a><span data-ttu-id="ab413-103">İçindeki kullanıcıları ve grupları yönetme :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab413-103">Manage users and groups in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="ab413-104">[Brennan Conroy](https://github.com/BrennanConroy) tarafından</span><span class="sxs-lookup"><span data-stu-id="ab413-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="ab413-105">:::no-loc(SignalR)::: iletilerin, belirli bir kullanıcıyla ve adlandırılmış bağlantı gruplarının yanı sıra ilişkili tüm bağlantılara gönderilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="ab413-105">:::no-loc(SignalR)::: allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="ab413-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ab413-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-no-locsignalr"></a><span data-ttu-id="ab413-107">İçindeki kullanıcılar :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab413-107">Users in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="ab413-108">Uygulamasındaki tek bir kullanıcının :::no-loc(SignalR)::: bir uygulamayla birden çok bağlantısı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ab413-108">A single user in :::no-loc(SignalR)::: can have multiple connections to an app.</span></span> <span data-ttu-id="ab413-109">Örneğin, bir Kullanıcı masaüstüne ve telefonlarına bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="ab413-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="ab413-110">Her cihazın ayrı bir :::no-loc(SignalR)::: bağlantısı vardır, ancak hepsi aynı kullanıcıyla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="ab413-110">Each device has a separate :::no-loc(SignalR)::: connection, but they're all associated with the same user.</span></span> <span data-ttu-id="ab413-111">Kullanıcıya bir ileti gönderildiğinde, bu kullanıcıyla ilişkili tüm bağlantılar iletiyi alır.</span><span class="sxs-lookup"><span data-stu-id="ab413-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="ab413-112">Bir bağlantı için Kullanıcı tanımlayıcısına, `Context.UserIdentifier` hub 'daki özellik tarafından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="ab413-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="ab413-113">Varsayılan olarak, :::no-loc(SignalR)::: `ClaimTypes.NameIdentifier` `ClaimsPrincipal` Kullanıcı tanımlayıcısı olarak bağlantıyla ilişkili öğesinden öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab413-113">By default, :::no-loc(SignalR)::: uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="ab413-114">Bu davranışı özelleştirmek için bkz. [kimlik işlemeyi özelleştirmek için talepleri kullanma](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="ab413-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="ab413-115">`User`Aşağıdaki örnekte gösterildiği gibi, kullanıcı tanımlayıcısını bir hub yöntemindeki işleve geçirerek belirli bir kullanıcıya bir ileti gönderin:</span><span class="sxs-lookup"><span data-stu-id="ab413-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="ab413-116">Kullanıcı tanımlayıcısı büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="ab413-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a><span data-ttu-id="ab413-117">İçindeki gruplar :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab413-117">Groups in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="ab413-118">Grup, bir adla ilişkili bir bağlantı koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="ab413-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="ab413-119">İletiler, bir gruptaki tüm bağlantılara gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="ab413-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="ab413-120">Gruplar uygulama tarafından yönetildiğinden, gruplar bir bağlantıya veya birden çok bağlantıya göndermek için önerilen yoldur.</span><span class="sxs-lookup"><span data-stu-id="ab413-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="ab413-121">Bir bağlantı, birden fazla grubun üyesi olabilir.</span><span class="sxs-lookup"><span data-stu-id="ab413-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="ab413-122">Gruplar, her odanın bir grup olarak gösterilebileceği bir sohbet uygulaması gibi bir şey için idealdir.</span><span class="sxs-lookup"><span data-stu-id="ab413-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="ab413-123">Ve yöntemleri aracılığıyla gruplara eklenen veya gruplardan bağlantılar kaldırılır `AddToGroupAsync` `RemoveFromGroupAsync` .</span><span class="sxs-lookup"><span data-stu-id="ab413-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="ab413-124">Bağlantı yeniden bağlandığında grup üyeliği korunmaz.</span><span class="sxs-lookup"><span data-stu-id="ab413-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="ab413-125">Bağlantı, yeniden oluşturulduğunda gruba yeniden katılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ab413-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="ab413-126">Uygulama birden çok sunucuya ölçeklendirildiyse bu bilgiler kullanılamadığından, bir grubun üyelerini saymak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="ab413-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="ab413-127">Grupları kullanırken kaynaklara erişimi korumak için ASP.NET Core [kimlik doğrulaması ve yetkilendirme](xref:signalr/authn-and-authz) işlevini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ab413-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="ab413-128">Bir Kullanıcı gruba yalnızca kimlik bilgileri bu grup için geçerli olduğunda eklenirse, bu gruba gönderilen iletiler yalnızca yetkili kullanıcılara gider.</span><span class="sxs-lookup"><span data-stu-id="ab413-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="ab413-129">Ancak, gruplar bir güvenlik özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="ab413-129">However, groups are not a security feature.</span></span> <span data-ttu-id="ab413-130">Kimlik doğrulama talepleri, süre sonu ve iptal gibi grupların olmadığı özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ab413-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="ab413-131">Kullanıcının gruba erişim izni iptal edildiğinde, uygulamanın kullanıcıyı gruptan açıkça kaldırması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ab413-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="ab413-132">Grup adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="ab413-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="ab413-133">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ab413-133">Related resources</span></span>

* [<span data-ttu-id="ab413-134">Kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="ab413-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ab413-135">Merkezler</span><span class="sxs-lookup"><span data-stu-id="ab413-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ab413-136">Azure 'da yayımlama</span><span class="sxs-lookup"><span data-stu-id="ab413-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
