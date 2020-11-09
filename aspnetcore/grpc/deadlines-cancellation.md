---
title: Son tarihleri ve iptali olan güvenilir gRPC Hizmetleri
author: jamesnk
description: .NET ' te son tarihler ve iptalle güvenilir gRPC Hizmetleri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059929"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="2e143-103">Son tarihleri ve iptali olan güvenilir gRPC Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="2e143-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="2e143-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2e143-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2e143-105">Son tarihleri ve iptali, gRPC istemcileri tarafından devam eden çağrıları durdurmak için kullanılan özelliklerdir.</span><span class="sxs-lookup"><span data-stu-id="2e143-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="2e143-106">Bu makalede, son tarihleri ve iptali önemli ve bunların .NET gRPC uygulamalarında nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2e143-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="2e143-107">Tarihi</span><span class="sxs-lookup"><span data-stu-id="2e143-107">Deadlines</span></span>

<span data-ttu-id="2e143-108">Son Tarih, bir gRPC istemcisinin, çağrının tamamlanmasını bekleyeceği süreyi belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e143-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="2e143-109">Son Tarih aşıldığında, çağrı iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="2e143-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="2e143-110">Bir çağrının ne kadar süreyle çalıştırılabildiğinden bir üst sınır sağladığından son tarih ayarlama önemlidir.</span><span class="sxs-lookup"><span data-stu-id="2e143-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="2e143-111">Yanlış çalışan hizmetlerin sürekli olarak çalıştırılmasını ve sunucu kaynaklarının tüketilmesi işlemini sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="2e143-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="2e143-112">Son tarihler, güvenilir uygulamalar oluşturmak için yararlı bir araçtır ve yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e143-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="2e143-113">Son Tarih yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="2e143-113">Deadline configuration:</span></span>

* <span data-ttu-id="2e143-114">Bir son tarih, `CallOptions.Deadline` bir çağrı yapıldığında kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2e143-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="2e143-115">Varsayılan son tarih değeri yok.</span><span class="sxs-lookup"><span data-stu-id="2e143-115">There is no default deadline value.</span></span> <span data-ttu-id="2e143-116">Son Tarih belirtilmediği takdirde gRPC çağrılarının süresi sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="2e143-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="2e143-117">Son Tarih, son tarihin aşılacağı UTC zamandır.</span><span class="sxs-lookup"><span data-stu-id="2e143-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="2e143-118">Örneğin, `DateTime.UtcNow.AddSeconds(5)` Şu anda 5 saniyelik bir son tarih.</span><span class="sxs-lookup"><span data-stu-id="2e143-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="2e143-119">Geçmişte veya geçerli bir zaman kullanılırsa, çağrı son tarihi de aşar.</span><span class="sxs-lookup"><span data-stu-id="2e143-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="2e143-120">Son Tarih, gRPC çağrısıyla birlikte gönderilir ve hem istemci hem de hizmet tarafından bağımsız olarak izlenir.</span><span class="sxs-lookup"><span data-stu-id="2e143-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="2e143-121">Bir tek makinede gRPC çağrısı tamamlanır, ancak yanıtın istemciye döndürdüğü zamana göre son tarih aşılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2e143-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="2e143-122">Son Tarih aşılırsa, istemci ve hizmetin farklı bir davranışı vardır:</span><span class="sxs-lookup"><span data-stu-id="2e143-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="2e143-123">İstemci, temel alınan HTTP isteğini hemen iptal eder ve bir `DeadlineExceeded` hata oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2e143-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="2e143-124">İstemci uygulaması, hatayı yakalamak ve kullanıcıya bir zaman aşımı iletisi göstermek için seçim yapabilir.</span><span class="sxs-lookup"><span data-stu-id="2e143-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="2e143-125">Sunucusunda, yürütülen HTTP isteği iptal edilir ve [Servercallcontext. CancellationToken](xref:System.Threading.CancellationToken) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2e143-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="2e143-126">HTTP isteği iptal edilse de, gRPC çağrısı, yöntem tamamlanana kadar sunucuda çalışmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="2e143-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="2e143-127">İptal belirtecinin çağrı ile birlikte iptal edilmesi için zaman uyumsuz yöntemlere geçirilmesi önemlidir.</span><span class="sxs-lookup"><span data-stu-id="2e143-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="2e143-128">Örneğin, zaman uyumsuz veritabanı sorgularına ve HTTP isteklerine bir iptal belirteci geçirme.</span><span class="sxs-lookup"><span data-stu-id="2e143-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="2e143-129">İptal belirtecinin geçirilmesi, iptal edilen çağrının sunucuda hızlı bir şekilde tamamlanmasını ve diğer çağrılar için kaynakların serbest geçirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e143-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="2e143-130">`CallOptions.Deadline`Bir gRPC çağrısının son tarihini ayarlamak için yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="2e143-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="2e143-131">`ServerCallContext.CancellationToken`GRPC hizmetinde kullanma:</span><span class="sxs-lookup"><span data-stu-id="2e143-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="2e143-132">Son tarihleri yayma</span><span class="sxs-lookup"><span data-stu-id="2e143-132">Propagating deadlines</span></span>

<span data-ttu-id="2e143-133">Çalıştırılan bir gRPC hizmetinden bir gRPC çağrısı yapıldığında, son tarih yayılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e143-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="2e143-134">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e143-134">For example:</span></span>

1. <span data-ttu-id="2e143-135">`FrontendService.GetUser`Son tarihi olan istemci uygulaması çağrıları.</span><span class="sxs-lookup"><span data-stu-id="2e143-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="2e143-136">`FrontendService` çağırır `UserService.GetUser` .</span><span class="sxs-lookup"><span data-stu-id="2e143-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="2e143-137">İstemci tarafından belirtilen son tarih, yeni gRPC çağrısıyla belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2e143-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="2e143-138">`UserService.GetUser` Son tarihi alır.</span><span class="sxs-lookup"><span data-stu-id="2e143-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="2e143-139">İstemci uygulamasının son tarihi aşılırsa, doğru zaman aşımına uğrar.</span><span class="sxs-lookup"><span data-stu-id="2e143-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="2e143-140">Çağrı bağlamı son tarihi şu şekilde sağlar `ServerCallContext.Deadline` :</span><span class="sxs-lookup"><span data-stu-id="2e143-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="2e143-141">Son tarihleri el ile yayma, mabera olabilir.</span><span class="sxs-lookup"><span data-stu-id="2e143-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="2e143-142">Son tarihin her çağrıya geçirilmesi gerekir ve yanlışlıkla kaçırılması kolaydır.</span><span class="sxs-lookup"><span data-stu-id="2e143-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="2e143-143">GRPC istemci fabrikası ile bir otomatik çözüm bulunur.</span><span class="sxs-lookup"><span data-stu-id="2e143-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="2e143-144">Belirtme `EnableCallContextPropagation` :</span><span class="sxs-lookup"><span data-stu-id="2e143-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="2e143-145">Son Tarih ve iptal belirtecini alt çağrılara otomatik olarak yayar.</span><span class="sxs-lookup"><span data-stu-id="2e143-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="2e143-146">, Karmaşık, iç içe geçmiş gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamaya yönelik mükemmel bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="2e143-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="2e143-147">Daha fazla bilgi için bkz. <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="2e143-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="2e143-148">İptal Etme</span><span class="sxs-lookup"><span data-stu-id="2e143-148">Cancellation</span></span>

<span data-ttu-id="2e143-149">İptal etme, gRPC istemcisinin artık gerekli olmayan uzun çalışan çağrıları iptal etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2e143-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="2e143-150">Örneğin, Kullanıcı bir Web sitesindeki sayfayı ziyaret ettiğinde gerçek zamanlı güncelleştirmeleri akıp bir gRPC çağrısı başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2e143-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="2e143-151">Kullanıcı sayfadan uzaklaştığında akış iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2e143-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="2e143-152">Bir gRPC çağrısı, [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) ile bir iptal belirteci geçirerek veya çağrıya çağrı yaparak istemcide iptal edilebilir `Dispose` .</span><span class="sxs-lookup"><span data-stu-id="2e143-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="2e143-153">iptal edilecek gRPC Hizmetleri:</span><span class="sxs-lookup"><span data-stu-id="2e143-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="2e143-154">`ServerCallContext.CancellationToken`Zaman uyumsuz yöntemlere geçirin.</span><span class="sxs-lookup"><span data-stu-id="2e143-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="2e143-155">Zaman uyumsuz yöntemlerin iptal edilmesi, sunucudaki çağrının hızlı bir şekilde tamamlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e143-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="2e143-156">İptal belirtecini alt çağrılara yay.</span><span class="sxs-lookup"><span data-stu-id="2e143-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="2e143-157">İptal belirtecini yayan alt çağrıların üst öğesiyle iptal edilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e143-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="2e143-158">[GRPC istemci fabrikası](xref:grpc/clientfactory) ve `EnableCallContextPropagation()` iptal belirtecini otomatik olarak yayar.</span><span class="sxs-lookup"><span data-stu-id="2e143-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e143-159">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e143-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
