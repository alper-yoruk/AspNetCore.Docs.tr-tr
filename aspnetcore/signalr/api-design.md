---
title: SignalRAPI tasarımı konuları
author: anurse
description: SignalRUygulamanızın sürümleri arasında uyumluluk için API 'leri tasarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: ef0285c611bd41d7fe686a4b370b6daae9be9174
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018994"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="f1555-103">SignalRAPI tasarımı konuları</span><span class="sxs-lookup"><span data-stu-id="f1555-103">SignalR API design considerations</span></span>

<span data-ttu-id="f1555-104">, [Andrew Stanton-nurte](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="f1555-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="f1555-105">Bu makalede, yapı SignalR tabanlı API 'ler için rehberlik sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f1555-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="f1555-106">Geriye dönük uyumluluk sağlamak için özel nesne parametreleri kullanın</span><span class="sxs-lookup"><span data-stu-id="f1555-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="f1555-107">Bir SignalR hub yöntemine (istemcide veya sunucuda) parametre eklenmesi, bir *son değişiklikten*sonra yapılır.</span><span class="sxs-lookup"><span data-stu-id="f1555-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="f1555-108">Bu, eski istemcilerin/sunucuların, yöntemi uygun sayıda parametre olmadan çağırmaya çalıştıklarında hata alabileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f1555-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="f1555-109">Ancak, özel bir nesne parametresine özellikler eklemek, bir son değişiklik **değildir** .</span><span class="sxs-lookup"><span data-stu-id="f1555-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="f1555-110">Bu, istemci veya sunucu üzerindeki değişikliklere dayanıklı olan uyumlu API 'Ler tasarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f1555-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="f1555-111">Örneğin, aşağıdaki gibi bir sunucu tarafı API göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="f1555-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="f1555-112">JavaScript istemcisi şu şekilde kullanarak bu yöntemi çağırır `invoke` :</span><span class="sxs-lookup"><span data-stu-id="f1555-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="f1555-113">Daha sonra sunucu yöntemine ikinci bir parametre eklerseniz, eski istemciler bu parametre değerini sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="f1555-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="f1555-114">Örnek:</span><span class="sxs-lookup"><span data-stu-id="f1555-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="f1555-115">Eski istemci bu yöntemi çağırmayı denediğinde şuna benzer bir hata olur:</span><span class="sxs-lookup"><span data-stu-id="f1555-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="f1555-116">Sunucusunda şöyle bir günlük iletisi görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="f1555-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="f1555-117">Eski istemci yalnızca bir parametre gönderdi, ancak yeni sunucu API 'SI iki parametre gerektirdi.</span><span class="sxs-lookup"><span data-stu-id="f1555-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="f1555-118">Özel nesneleri parametre olarak kullanmak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1555-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="f1555-119">Özgün API 'yi özel bir nesne kullanacak şekilde yeniden tasarlayalım:</span><span class="sxs-lookup"><span data-stu-id="f1555-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="f1555-120">Artık istemci, yöntemi çağırmak için bir nesne kullanır:</span><span class="sxs-lookup"><span data-stu-id="f1555-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="f1555-121">Bir parametre eklemek yerine, nesnesine bir özellik ekleyin `TotalLengthRequest` :</span><span class="sxs-lookup"><span data-stu-id="f1555-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="f1555-122">Eski istemci tek bir parametre gönderdiğinde, ek `Param2` özellik bırakılır `null` .</span><span class="sxs-lookup"><span data-stu-id="f1555-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="f1555-123">`Param2` `null` ' İ denetleyerek ve varsayılan bir değer uygulayarak, eski bir istemci tarafından gönderilen bir iletiyi tespit edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f1555-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="f1555-124">Yeni bir istemci her iki parametreyi de gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="f1555-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="f1555-125">Aynı teknik, istemcide tanımlanan yöntemler için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1555-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="f1555-126">Sunucu tarafında özel bir nesne gönderebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f1555-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="f1555-127">İstemci tarafında, `Message` bir parametre kullanmak yerine özelliğe erişirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f1555-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="f1555-128">Daha sonra iletinin gönderisini yüke eklemeye karar verirseniz, nesnesine bir özellik ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f1555-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="f1555-129">Eski istemciler `Sender` değeri beklemdiklerinden, yok sayacaktır.</span><span class="sxs-lookup"><span data-stu-id="f1555-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="f1555-130">Yeni bir istemci, yeni özelliği okumak üzere güncelleyerek kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="f1555-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="f1555-131">Bu durumda, yeni istemci Ayrıca değer sağlamayan eski bir sunucu için de toleranslı olur `Sender` .</span><span class="sxs-lookup"><span data-stu-id="f1555-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="f1555-132">Eski sunucu `Sender` değeri sağlamadığından, istemci bu değere erişmeden önce varolup olmadığını kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="f1555-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
