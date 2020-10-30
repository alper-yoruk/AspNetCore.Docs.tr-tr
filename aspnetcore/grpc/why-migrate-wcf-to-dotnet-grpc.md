---
title: WCF neden ASP.NET Core gRPC’ye geçirilmeli?
author: markrendle
description: Bu makalede, ASP.NET Core gRPC 'nin neden modern mimarilere ve platformlara geçiş yapmak isteyen Windows Communication Foundation (WCF) geliştiricileri için uygun bir Özet olduğunu bulabilirsiniz.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058694"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="d04d9-103">Windows Communication Foundation için gRPC (WCF) geliştiricileri</span><span class="sxs-lookup"><span data-stu-id="d04d9-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="d04d9-104">Bu makalede, ASP.NET Core gRPC 'nin neden modern mimarilere ve platformlara geçiş yapmak isteyen Windows Communication Foundation (WCF) geliştiricileri için uygun bir Özet olduğunu bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d04d9-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="d04d9-105">WCF ile karşılaştırma</span><span class="sxs-lookup"><span data-stu-id="d04d9-105">Comparison to WCF</span></span>

<span data-ttu-id="d04d9-106">Uygulama ve yaklaşım gRPC için farklı olsa da, gRPC ile hizmetleri geliştirme ve kullanma deneyimi WCF geliştiricileri için sezgisel olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="d04d9-107">WCF ve gRPC, aynı hedeflere sahip RPC (uzak yordam çağrısı) çerçevelerdir:</span><span class="sxs-lookup"><span data-stu-id="d04d9-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="d04d9-108">İstemci ve sunucu aynı platformda olsa da kodun kodda olmasını mümkün hale getirin.</span><span class="sxs-lookup"><span data-stu-id="d04d9-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="d04d9-109">Basitleştirilmiş bir taşınabilir ağ API 'SI sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d04d9-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="d04d9-110">Her iki platform da arabirim bildirme ve uygulama gereksinimini paylaşır, ancak arabirimi bildirme işlemi farklıdır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="d04d9-111">GRPC 'nin, WCF Hizmetleri için kullanılabilen bağlamalara iyi eşleme desteklediği birçok RPC çağrısı türü.</span><span class="sxs-lookup"><span data-stu-id="d04d9-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="d04d9-112">Daha fazla bilgi ve örnek için bkz. [WCF çözümünü gRPC 'ye geçirme](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="d04d9-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="d04d9-113">GRPC 'nin avantajları</span><span class="sxs-lookup"><span data-stu-id="d04d9-113">Benefits of gRPC</span></span>

<span data-ttu-id="d04d9-114">gRPC aşağıdaki nedenlerden dolayı diğer yaklaşımlardan daha iyi bir çerçeve sağlar.</span><span class="sxs-lookup"><span data-stu-id="d04d9-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="d04d9-115">Performans</span><span class="sxs-lookup"><span data-stu-id="d04d9-115">Performance</span></span>

<span data-ttu-id="d04d9-116">gRPC, HTTP/2 kullanır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="d04d9-117">HTTP/1.1 'nin aksine, HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="d04d9-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="d04d9-118">Daha küçük, daha hızlı bir ikili protokoldür.</span><span class="sxs-lookup"><span data-stu-id="d04d9-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="d04d9-119">, Bilgisayarların ayrıştırılmasında daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="d04d9-120">Tek bir bağlantı üzerinden çoğullama isteklerini destekler.</span><span class="sxs-lookup"><span data-stu-id="d04d9-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="d04d9-121">Çoğullama, birden çok isteğin bir bağlantı üzerinden, birbirini engelleyen istekler olmadan gönderilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d04d9-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="d04d9-122">HTTP/1.1 ' de, engelleme "satır başı (HOL) engelleme" olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="d04d9-123">gRPC, iletileri seri hale getirmek için etkin bir ikili biçimi olan Protoarabelleği kullanır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="d04d9-124">Prototipsiz iletiler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d04d9-124">Protobuf messages are:</span></span>
* <span data-ttu-id="d04d9-125">Serileştirme ve seri durumdan çıkarma hızlı.</span><span class="sxs-lookup"><span data-stu-id="d04d9-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="d04d9-126">Metin tabanlı biçimlerden daha az bant genişliği kullanın.</span><span class="sxs-lookup"><span data-stu-id="d04d9-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="d04d9-127">gRPC, bant genişliği kısıtlamalarına sahip mobil cihazlar ve ağlar için iyi bir çözümdür.</span><span class="sxs-lookup"><span data-stu-id="d04d9-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="d04d9-128">Birlikte çalışabilirlik</span><span class="sxs-lookup"><span data-stu-id="d04d9-128">Interoperability</span></span>

<span data-ttu-id="d04d9-129">.NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby ve PHP dahil olmak üzere tüm önemli programlama dilleri ve platformları için gRPC araçları ve kitaplıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="d04d9-130">Her platform için Prototipsiz ikili kablo biçimi ve verimli kod üretimi sayesinde geliştiriciler platformlar arası, performanslı uygulamalar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="d04d9-131">Kullanılabilirlik ve üretkenlik</span><span class="sxs-lookup"><span data-stu-id="d04d9-131">Usability and productivity</span></span>

<span data-ttu-id="d04d9-132">gRPC, kapsamlı bir RPC çözümüdür.</span><span class="sxs-lookup"><span data-stu-id="d04d9-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="d04d9-133">Birden çok dil ve platformda sürekli olarak işe yarar.</span><span class="sxs-lookup"><span data-stu-id="d04d9-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="d04d9-134">Ayrıca, ortak kodların büyük bölümü otomatik olarak oluşturulan mükemmel araçlar da sağlar.</span><span class="sxs-lookup"><span data-stu-id="d04d9-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="d04d9-135">WCF gibi gRPC, iletileri ve kesin olarak belirlenmiş bir istemciyi otomatik olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d04d9-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="d04d9-136">Geliştirici saati, iş mantığına odaklanmak için serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="d04d9-137">Akış</span><span class="sxs-lookup"><span data-stu-id="d04d9-137">Streaming</span></span>

<span data-ttu-id="d04d9-138">gRPC 'de, WCF 'nin tam çift yönlü hizmetlerine benzer işlevler sağlayan tam çift yönlü akış vardır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="d04d9-139">gRPC akışı, normal internet bağlantıları, yük dengeleyiciler ve hizmet kafesleri üzerinden çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="d04d9-140">Son tarihler, zaman aşımları ve iptal</span><span class="sxs-lookup"><span data-stu-id="d04d9-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="d04d9-141">gRPC, istemcilerin bir RPC 'nin tamamlaması için en uzun süreyi belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d04d9-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="d04d9-142">Belirtilen son tarih aşılırsa sunucu, işlemi istemciden bağımsız olarak iptal edebilir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="d04d9-143">Son tarihler ve iptaller, kaynak kullanım sınırlarını zorlamaya yardımcı olmak için sonraki gRPC çağrıları aracılığıyla yayılamaz.</span><span class="sxs-lookup"><span data-stu-id="d04d9-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="d04d9-144">İstemciler, son tarih aşıldığında veya gerekirse daha önceki işlemleri durdurabilir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="d04d9-145">Örneğin, istemciler Kullanıcı etkileşimi nedeniyle işlemleri durdurabilir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="d04d9-146">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="d04d9-146">Security</span></span>

<span data-ttu-id="d04d9-147">gRPC, istemci ve sunucu arasında uçtan uca şifreli bir bağlantı sağlamak için TLS ve HTTP/2 kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="d04d9-148">İstemci sertifikası kimlik doğrulaması desteği, istemci ve sunucu arasındaki güvenliği ve güveni daha da artırır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="d04d9-149">WCF için .NET Core ve .NET 5 ' e geçiş yolu olarak gRPC</span><span class="sxs-lookup"><span data-stu-id="d04d9-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="d04d9-150">.NET Core ve .NET 5, Microsoft 'un bir dizi platformda hizmet sunmak isteyen geliştiricilere uzaktan iletişim çözümleri sunma biçiminde bir kaydırma işareti işaretler.</span><span class="sxs-lookup"><span data-stu-id="d04d9-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="d04d9-151">.NET Core ve .NET 5, [WCF hizmetlerini çağırmayı](/dotnet/core/additional-tools/wcf-web-service-reference-guide)destekler, ancak WCF barındırma için sunucu tarafı desteği sunmaz.</span><span class="sxs-lookup"><span data-stu-id="d04d9-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="d04d9-152">WCF uygulamalarını modernize etmek için önerilen iki yol vardır:</span><span class="sxs-lookup"><span data-stu-id="d04d9-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="d04d9-153">gRPC, modern teknolojiler üzerine kurulmuştur ve RPC uygulamalarına yönelik geliştirici topluluğu genelinde en popüler seçim olarak ortaya çıktı.</span><span class="sxs-lookup"><span data-stu-id="d04d9-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="d04d9-154">.NET Core 3,0 ile başlayarak, modern .NET platformları gRPC için harika desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="d04d9-155">WCF hizmetlerinin gRPC kullanacak şekilde geçirilmesi, Modern uygulamalarda gerekli olan RPC özelliklerinin, performansının sağlanmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d04d9-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="d04d9-156">[Corewcf](https://github.com/CoreWCF/CoreWCF) , WCF hizmetlerinin .NET Core ve .NET 5 ' e barındırılmasına yönelik destek almak için bir topluluk çabasında bulunur.</span><span class="sxs-lookup"><span data-stu-id="d04d9-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="d04d9-157">Bir önizleme sürümü kullanılabilir ve proje, üretime hazır olma aşamasında çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="d04d9-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="d04d9-158">CoreWCF yalnızca WCF özelliklerinin bir alt kümesini destekler ve bunu kullanmak için geçiş yapan .NET Framework uygulamalar, kod değişiklikleri ve test işleminin başarılı olması için gerekli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="d04d9-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="d04d9-159">Bir uygulama, WCF Hizmetleri çağıran mevcut istemcilerle uyumluluğu sürdürmek için gerekliyse, CoreWCF iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="d04d9-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="d04d9-160">başlarken</span><span class="sxs-lookup"><span data-stu-id="d04d9-160">Get started</span></span>

<span data-ttu-id="d04d9-161">WCF geliştiricileri için ASP.NET Core gRPC hizmetleri oluşturma hakkında ayrıntılı yönergeler için bkz. [WCF geliştiricileri için ASP.NET Core gRPC](/dotnet/architecture/grpc-for-wcf-developers)</span><span class="sxs-lookup"><span data-stu-id="d04d9-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
