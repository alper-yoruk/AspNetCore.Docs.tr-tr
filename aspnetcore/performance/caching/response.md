---
<span data-ttu-id="6f462-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-102">'Blazor'</span></span>
- <span data-ttu-id="6f462-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-103">'Identity'</span></span>
- <span data-ttu-id="6f462-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-105">'Razor'</span></span>
- <span data-ttu-id="6f462-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="6f462-107">ASP.NET Core 'de yanıt önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="6f462-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="6f462-108">[John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="6f462-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6f462-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6f462-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6f462-110">Yanıt önbelleğe alma, bir istemcinin veya proxy 'nin bir Web sunucusunda yaptığı istek sayısını azaltır.</span><span class="sxs-lookup"><span data-stu-id="6f462-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="6f462-111">Yanıt önbelleği Ayrıca, Web sunucusunun yanıt oluşturmak için gerçekleştirdiği iş miktarını azaltır.</span><span class="sxs-lookup"><span data-stu-id="6f462-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="6f462-112">Yanıt önbelleğe alma, istemci, ara sunucu ve ara yazılım 'nin yanıtları nasıl önbellekte istediğinizi belirten üstbilgiler tarafından denetlenir.</span><span class="sxs-lookup"><span data-stu-id="6f462-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="6f462-113">[Responsecache özniteliği](#responsecache-attribute) , yanıt önbelleği üst bilgilerini ayarlamaya katılır.</span><span class="sxs-lookup"><span data-stu-id="6f462-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="6f462-114">İstemciler ve ara proxy 'ler, [HTTP 1,1 önbelleğe alma belirtiminde](https://tools.ietf.org/html/rfc7234)yanıtları önbelleğe almak için üst bilgileri dikkate almalıdır.</span><span class="sxs-lookup"><span data-stu-id="6f462-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="6f462-115">HTTP 1,1 önbelleğe alma belirtimini izleyen sunucu tarafında önbelleğe alma için [yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware)' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6f462-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="6f462-116">Ara yazılım, <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> sunucu tarafı önbelleğe alma davranışını etkilemek için özellikleri kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="6f462-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="6f462-117">HTTP tabanlı yanıt önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="6f462-117">HTTP-based response caching</span></span>

<span data-ttu-id="6f462-118">[HTTP 1,1 önbelleğe alma belirtimi](https://tools.ietf.org/html/rfc7234) , Internet önbelleklerinin nasıl davranması gerektiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="6f462-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="6f462-119">Önbelleğe alma için kullanılan birincil HTTP üst bilgisi cache [-Control](https://tools.ietf.org/html/rfc7234#section-5.2)' dır, önbellek *yönergeleri*belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6f462-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="6f462-120">Yönergeler denetim önbelleği, istek olarak önbelleğe alma davranışını istemcilerden sunuculara ve yanıt olarak sunuculardan istemcilere geri doğru bir şekilde getirir.</span><span class="sxs-lookup"><span data-stu-id="6f462-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="6f462-121">İstekler ve yanıtlar proxy sunucuları üzerinden taşınır ve proxy sunucuları da HTTP 1,1 önbelleğe alma belirtimine uymalıdır.</span><span class="sxs-lookup"><span data-stu-id="6f462-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="6f462-122">Ortak `Cache-Control` yönergeler aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6f462-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="6f462-123">Deki</span><span class="sxs-lookup"><span data-stu-id="6f462-123">Directive</span></span>                                                       | <span data-ttu-id="6f462-124">Eylem</span><span class="sxs-lookup"><span data-stu-id="6f462-124">Action</span></span> |
| ---
<span data-ttu-id="6f462-125">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-126">'Blazor'</span></span>
- <span data-ttu-id="6f462-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-127">'Identity'</span></span>
- <span data-ttu-id="6f462-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-129">'Razor'</span></span>
- <span data-ttu-id="6f462-130">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-131">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-132">'Blazor'</span></span>
- <span data-ttu-id="6f462-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-133">'Identity'</span></span>
- <span data-ttu-id="6f462-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-135">'Razor'</span></span>
- <span data-ttu-id="6f462-136">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-137">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-138">'Blazor'</span></span>
- <span data-ttu-id="6f462-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-139">'Identity'</span></span>
- <span data-ttu-id="6f462-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-141">'Razor'</span></span>
- <span data-ttu-id="6f462-142">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-143">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-144">'Blazor'</span></span>
- <span data-ttu-id="6f462-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-145">'Identity'</span></span>
- <span data-ttu-id="6f462-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-147">'Razor'</span></span>
- <span data-ttu-id="6f462-148">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-149">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-150">'Blazor'</span></span>
- <span data-ttu-id="6f462-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-151">'Identity'</span></span>
- <span data-ttu-id="6f462-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-153">'Razor'</span></span>
- <span data-ttu-id="6f462-154">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-155">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-156">'Blazor'</span></span>
- <span data-ttu-id="6f462-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-157">'Identity'</span></span>
- <span data-ttu-id="6f462-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-159">'Razor'</span></span>
- <span data-ttu-id="6f462-160">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-161">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-162">'Blazor'</span></span>
- <span data-ttu-id="6f462-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-163">'Identity'</span></span>
- <span data-ttu-id="6f462-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-165">'Razor'</span></span>
- <span data-ttu-id="6f462-166">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-167">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-168">'Blazor'</span></span>
- <span data-ttu-id="6f462-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-169">'Identity'</span></span>
- <span data-ttu-id="6f462-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-171">'Razor'</span></span>
- <span data-ttu-id="6f462-172">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-173">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-174">'Blazor'</span></span>
- <span data-ttu-id="6f462-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-175">'Identity'</span></span>
- <span data-ttu-id="6f462-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-177">'Razor'</span></span>
- <span data-ttu-id="6f462-178">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-179">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-180">'Blazor'</span></span>
- <span data-ttu-id="6f462-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-181">'Identity'</span></span>
- <span data-ttu-id="6f462-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-183">'Razor'</span></span>
- <span data-ttu-id="6f462-184">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-185">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-186">'Blazor'</span></span>
- <span data-ttu-id="6f462-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-187">'Identity'</span></span>
- <span data-ttu-id="6f462-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-189">'Razor'</span></span>
- <span data-ttu-id="6f462-190">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-191">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-192">'Blazor'</span></span>
- <span data-ttu-id="6f462-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-193">'Identity'</span></span>
- <span data-ttu-id="6f462-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-195">'Razor'</span></span>
- <span data-ttu-id="6f462-196">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-197">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-198">'Blazor'</span></span>
- <span data-ttu-id="6f462-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-199">'Identity'</span></span>
- <span data-ttu-id="6f462-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-201">'Razor'</span></span>
- <span data-ttu-id="6f462-202">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-203">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-204">'Blazor'</span></span>
- <span data-ttu-id="6f462-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-205">'Identity'</span></span>
- <span data-ttu-id="6f462-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-207">'Razor'</span></span>
- <span data-ttu-id="6f462-208">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-209">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-210">'Blazor'</span></span>
- <span data-ttu-id="6f462-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-211">'Identity'</span></span>
- <span data-ttu-id="6f462-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-213">'Razor'</span></span>
- <span data-ttu-id="6f462-214">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-215">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-216">'Blazor'</span></span>
- <span data-ttu-id="6f462-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-217">'Identity'</span></span>
- <span data-ttu-id="6f462-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-219">'Razor'</span></span>
- <span data-ttu-id="6f462-220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-221">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-222">'Blazor'</span></span>
- <span data-ttu-id="6f462-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-223">'Identity'</span></span>
- <span data-ttu-id="6f462-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-225">'Razor'</span></span>
- <span data-ttu-id="6f462-226">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-227">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-228">'Blazor'</span></span>
- <span data-ttu-id="6f462-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-229">'Identity'</span></span>
- <span data-ttu-id="6f462-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-231">'Razor'</span></span>
- <span data-ttu-id="6f462-232">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-233">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-234">'Blazor'</span></span>
- <span data-ttu-id="6f462-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-235">'Identity'</span></span>
- <span data-ttu-id="6f462-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-237">'Razor'</span></span>
- <span data-ttu-id="6f462-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-239">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-240">'Blazor'</span></span>
- <span data-ttu-id="6f462-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-241">'Identity'</span></span>
- <span data-ttu-id="6f462-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-243">'Razor'</span></span>
- <span data-ttu-id="6f462-244">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-245">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-246">'Blazor'</span></span>
- <span data-ttu-id="6f462-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-247">'Identity'</span></span>
- <span data-ttu-id="6f462-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-249">'Razor'</span></span>
- <span data-ttu-id="6f462-250">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-251">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-252">'Blazor'</span></span>
- <span data-ttu-id="6f462-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-253">'Identity'</span></span>
- <span data-ttu-id="6f462-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-255">'Razor'</span></span>
- <span data-ttu-id="6f462-256">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-257">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-258">'Blazor'</span></span>
- <span data-ttu-id="6f462-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-259">'Identity'</span></span>
- <span data-ttu-id="6f462-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-261">'Razor'</span></span>
- <span data-ttu-id="6f462-262">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-263">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-264">'Blazor'</span></span>
- <span data-ttu-id="6f462-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-265">'Identity'</span></span>
- <span data-ttu-id="6f462-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-267">'Razor'</span></span>
- <span data-ttu-id="6f462-268">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-269">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-270">'Blazor'</span></span>
- <span data-ttu-id="6f462-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-271">'Identity'</span></span>
- <span data-ttu-id="6f462-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-273">'Razor'</span></span>
- <span data-ttu-id="6f462-274">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-275">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-276">'Blazor'</span></span>
- <span data-ttu-id="6f462-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-277">'Identity'</span></span>
- <span data-ttu-id="6f462-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-279">'Razor'</span></span>
- <span data-ttu-id="6f462-280">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-281">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-282">'Blazor'</span></span>
- <span data-ttu-id="6f462-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-283">'Identity'</span></span>
- <span data-ttu-id="6f462-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-285">'Razor'</span></span>
- <span data-ttu-id="6f462-286">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-287">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-288">'Blazor'</span></span>
- <span data-ttu-id="6f462-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-289">'Identity'</span></span>
- <span data-ttu-id="6f462-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-291">'Razor'</span></span>
- <span data-ttu-id="6f462-292">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-293">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-294">'Blazor'</span></span>
- <span data-ttu-id="6f462-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-295">'Identity'</span></span>
- <span data-ttu-id="6f462-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-297">'Razor'</span></span>
- <span data-ttu-id="6f462-298">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-298">'SignalR' uid:</span></span> 

<span data-ttu-id="6f462-299">-------------------------------- | ---başlığı: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-300">'Blazor'</span></span>
- <span data-ttu-id="6f462-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-301">'Identity'</span></span>
- <span data-ttu-id="6f462-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-303">'Razor'</span></span>
- <span data-ttu-id="6f462-304">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-304">'SignalR' uid:</span></span> 

<span data-ttu-id="6f462-305">--- | | [genel](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Önbellek, yanıtı saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="6f462-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="6f462-306">| | [özel](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | Yanıtın paylaşılan bir önbellek tarafından depolanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6f462-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="6f462-307">Özel bir önbellek, yanıtı depolayıp yeniden kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="6f462-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="6f462-308">| | [en fazla yaş](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | İstemci, yaşı belirtilen saniyeden daha büyük olan bir yanıtı kabul etmez.</span><span class="sxs-lookup"><span data-stu-id="6f462-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="6f462-309">Örnekler: `max-age=60` (60 saniye), `max-age=2592000` (1 ay) | | [önbellek yok](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **İsteklerde**: bir önbellek, isteği karşılamak için depolanan bir yanıt kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="6f462-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="6f462-310">Kaynak sunucu, istemcinin yanıtını yeniden oluşturur ve ara yazılım, depolanan yanıtı önbelleğinde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="6f462-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="6f462-311">**Yanıtlar**: yanıt, kaynak sunucuda doğrulamadan sonraki bir istek için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="6f462-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="6f462-312">| | [mağaza yok](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **Istekler üzerinde**: bir önbelleğin isteği depolaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6f462-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="6f462-313">**Yanıtlar**: bir önbellek, yanıtın herhangi bir parçasını depolamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="6f462-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="6f462-314">Önbelleğe alma işleminde bir rol oynatacak diğer önbellek üstbilgileri aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6f462-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="6f462-315">Üst bilgi</span><span class="sxs-lookup"><span data-stu-id="6f462-315">Header</span></span>                                                     | <span data-ttu-id="6f462-316">İşlev</span><span class="sxs-lookup"><span data-stu-id="6f462-316">Function</span></span> |
| ---
<span data-ttu-id="6f462-317">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-318">'Blazor'</span></span>
- <span data-ttu-id="6f462-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-319">'Identity'</span></span>
- <span data-ttu-id="6f462-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-321">'Razor'</span></span>
- <span data-ttu-id="6f462-322">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-323">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-324">'Blazor'</span></span>
- <span data-ttu-id="6f462-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-325">'Identity'</span></span>
- <span data-ttu-id="6f462-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-327">'Razor'</span></span>
- <span data-ttu-id="6f462-328">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-329">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-330">'Blazor'</span></span>
- <span data-ttu-id="6f462-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-331">'Identity'</span></span>
- <span data-ttu-id="6f462-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-333">'Razor'</span></span>
- <span data-ttu-id="6f462-334">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-335">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-336">'Blazor'</span></span>
- <span data-ttu-id="6f462-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-337">'Identity'</span></span>
- <span data-ttu-id="6f462-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-339">'Razor'</span></span>
- <span data-ttu-id="6f462-340">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-341">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-342">'Blazor'</span></span>
- <span data-ttu-id="6f462-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-343">'Identity'</span></span>
- <span data-ttu-id="6f462-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-345">'Razor'</span></span>
- <span data-ttu-id="6f462-346">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-347">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-348">'Blazor'</span></span>
- <span data-ttu-id="6f462-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-349">'Identity'</span></span>
- <span data-ttu-id="6f462-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-351">'Razor'</span></span>
- <span data-ttu-id="6f462-352">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-353">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-354">'Blazor'</span></span>
- <span data-ttu-id="6f462-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-355">'Identity'</span></span>
- <span data-ttu-id="6f462-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-357">'Razor'</span></span>
- <span data-ttu-id="6f462-358">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-359">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-360">'Blazor'</span></span>
- <span data-ttu-id="6f462-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-361">'Identity'</span></span>
- <span data-ttu-id="6f462-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-363">'Razor'</span></span>
- <span data-ttu-id="6f462-364">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-365">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-366">'Blazor'</span></span>
- <span data-ttu-id="6f462-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-367">'Identity'</span></span>
- <span data-ttu-id="6f462-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-369">'Razor'</span></span>
- <span data-ttu-id="6f462-370">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-371">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-372">'Blazor'</span></span>
- <span data-ttu-id="6f462-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-373">'Identity'</span></span>
- <span data-ttu-id="6f462-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-375">'Razor'</span></span>
- <span data-ttu-id="6f462-376">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-377">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-378">'Blazor'</span></span>
- <span data-ttu-id="6f462-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-379">'Identity'</span></span>
- <span data-ttu-id="6f462-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-381">'Razor'</span></span>
- <span data-ttu-id="6f462-382">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-383">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-384">'Blazor'</span></span>
- <span data-ttu-id="6f462-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-385">'Identity'</span></span>
- <span data-ttu-id="6f462-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-387">'Razor'</span></span>
- <span data-ttu-id="6f462-388">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-389">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-390">'Blazor'</span></span>
- <span data-ttu-id="6f462-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-391">'Identity'</span></span>
- <span data-ttu-id="6f462-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-393">'Razor'</span></span>
- <span data-ttu-id="6f462-394">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-395">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-396">'Blazor'</span></span>
- <span data-ttu-id="6f462-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-397">'Identity'</span></span>
- <span data-ttu-id="6f462-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-399">'Razor'</span></span>
- <span data-ttu-id="6f462-400">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-401">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-402">'Blazor'</span></span>
- <span data-ttu-id="6f462-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-403">'Identity'</span></span>
- <span data-ttu-id="6f462-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-405">'Razor'</span></span>
- <span data-ttu-id="6f462-406">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-407">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-408">'Blazor'</span></span>
- <span data-ttu-id="6f462-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-409">'Identity'</span></span>
- <span data-ttu-id="6f462-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-411">'Razor'</span></span>
- <span data-ttu-id="6f462-412">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-413">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-414">'Blazor'</span></span>
- <span data-ttu-id="6f462-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-415">'Identity'</span></span>
- <span data-ttu-id="6f462-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-417">'Razor'</span></span>
- <span data-ttu-id="6f462-418">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-419">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-420">'Blazor'</span></span>
- <span data-ttu-id="6f462-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-421">'Identity'</span></span>
- <span data-ttu-id="6f462-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-423">'Razor'</span></span>
- <span data-ttu-id="6f462-424">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-425">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-426">'Blazor'</span></span>
- <span data-ttu-id="6f462-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-427">'Identity'</span></span>
- <span data-ttu-id="6f462-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-429">'Razor'</span></span>
- <span data-ttu-id="6f462-430">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-431">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-432">'Blazor'</span></span>
- <span data-ttu-id="6f462-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-433">'Identity'</span></span>
- <span data-ttu-id="6f462-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-435">'Razor'</span></span>
- <span data-ttu-id="6f462-436">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-437">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-438">'Blazor'</span></span>
- <span data-ttu-id="6f462-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-439">'Identity'</span></span>
- <span data-ttu-id="6f462-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-441">'Razor'</span></span>
- <span data-ttu-id="6f462-442">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-443">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-444">'Blazor'</span></span>
- <span data-ttu-id="6f462-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-445">'Identity'</span></span>
- <span data-ttu-id="6f462-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-447">'Razor'</span></span>
- <span data-ttu-id="6f462-448">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-449">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-450">'Blazor'</span></span>
- <span data-ttu-id="6f462-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-451">'Identity'</span></span>
- <span data-ttu-id="6f462-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-453">'Razor'</span></span>
- <span data-ttu-id="6f462-454">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-455">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-456">'Blazor'</span></span>
- <span data-ttu-id="6f462-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-457">'Identity'</span></span>
- <span data-ttu-id="6f462-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-459">'Razor'</span></span>
- <span data-ttu-id="6f462-460">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-461">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-462">'Blazor'</span></span>
- <span data-ttu-id="6f462-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-463">'Identity'</span></span>
- <span data-ttu-id="6f462-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-465">'Razor'</span></span>
- <span data-ttu-id="6f462-466">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-467">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-468">'Blazor'</span></span>
- <span data-ttu-id="6f462-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-469">'Identity'</span></span>
- <span data-ttu-id="6f462-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-471">'Razor'</span></span>
- <span data-ttu-id="6f462-472">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-473">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-474">'Blazor'</span></span>
- <span data-ttu-id="6f462-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-475">'Identity'</span></span>
- <span data-ttu-id="6f462-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-477">'Razor'</span></span>
- <span data-ttu-id="6f462-478">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-478">'SignalR' uid:</span></span> 

<span data-ttu-id="6f462-479">----------------------------- | ---başlığı: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-480">'Blazor'</span></span>
- <span data-ttu-id="6f462-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-481">'Identity'</span></span>
- <span data-ttu-id="6f462-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-483">'Razor'</span></span>
- <span data-ttu-id="6f462-484">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-485">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-486">'Blazor'</span></span>
- <span data-ttu-id="6f462-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-487">'Identity'</span></span>
- <span data-ttu-id="6f462-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-489">'Razor'</span></span>
- <span data-ttu-id="6f462-490">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-490">'SignalR' uid:</span></span> 

<span data-ttu-id="6f462-491">---- | | [Yaş](https://tools.ietf.org/html/rfc7234#section-5.1) | Yanıt oluşturulduktan veya kaynak sunucuda başarıyla doğrulandıktan sonra geçen sürenin saniye cinsinden tahmini.</span><span class="sxs-lookup"><span data-stu-id="6f462-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="6f462-492">| | [Süre sonu](https://tools.ietf.org/html/rfc7234#section-5.3) | Yanıtın eski kabul edildiği zaman.</span><span class="sxs-lookup"><span data-stu-id="6f462-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="6f462-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Ayar davranışı için HTTP/1.0 önbellekler ile geriye dönük uyumluluk için mevcuttur `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="6f462-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="6f462-494">`Cache-Control`Üst bilgi varsa, `Pragma` üst bilgi yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="6f462-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="6f462-495">| | [Farklılık](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Tüm `Vary` üst bilgi alanları önbelleğe alınan yanıtın orijinal isteği ve yeni istek ile eşleşmediği takdirde, önbelleğe alınmış bir yanıtın gönderilmemesi gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6f462-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="6f462-496">HTTP tabanlı önbelleğe alma istek önbelleği-denetim yönergeleri</span><span class="sxs-lookup"><span data-stu-id="6f462-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="6f462-497">[Cache-Control üst bilgisi Için HTTP 1,1 önbelleğe alma belirtiminin](https://tools.ietf.org/html/rfc7234#section-5.2) , istemci tarafından gönderilen geçerli bir üst bilgiyi kabul etmek için bir önbellek gerekir `Cache-Control` .</span><span class="sxs-lookup"><span data-stu-id="6f462-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="6f462-498">İstemci `no-cache` , bir üst bilgi değeri ile istek yapabilir ve sunucuyu her istek için yeni bir yanıt oluşturmaya zorlayabilir.</span><span class="sxs-lookup"><span data-stu-id="6f462-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="6f462-499">İstemci `Cache-Control` isteği üst bilgilerinin her zaman dikkate alınması, http önbelleği hedefini düşünüyorsanız anlamlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="6f462-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="6f462-500">Resmi belirtim altında, önbelleğe alma, istemcilerin, proxy 'lerin ve sunucuların bir ağı üzerinde istekleri karşılayan gecikme süresini ve ağ yükünü azaltmaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="6f462-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="6f462-501">Kaynak sunucu üzerindeki yükü denetlemek için bir yol değildir.</span><span class="sxs-lookup"><span data-stu-id="6f462-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="6f462-502">Yazılım, resmi önbellek belirtimine bağlı olduğundan, [yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) kullanılırken bu önbelleğe alma davranışının üzerinde geliştirici denetimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="6f462-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="6f462-503">[Ara yazılım Için planlı geliştirmeler](https://github.com/dotnet/AspNetCore/issues/2612) , `Cache-Control` önbelleğe alınmış bir yanıt sunmaya karar verirken bir isteğin üst bilgisini yoksayacak şekilde yapılandırmak için bir fırsattır.</span><span class="sxs-lookup"><span data-stu-id="6f462-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="6f462-504">Planlı geliştirmeler sunucu yükünü daha iyi denetleme olanağı sağlar.</span><span class="sxs-lookup"><span data-stu-id="6f462-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="6f462-505">ASP.NET Core diğer önbelleğe alma teknolojisi</span><span class="sxs-lookup"><span data-stu-id="6f462-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="6f462-506">Bellek içi önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="6f462-506">In-memory caching</span></span>

<span data-ttu-id="6f462-507">Bellek içi önbelleğe alma, önbelleğe alınmış verileri depolamak için sunucu belleğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6f462-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="6f462-508">Bu tür bir önbelleğe alma, *yapışkan oturumları*kullanan tek bir sunucu veya birden çok sunucu için uygundur.</span><span class="sxs-lookup"><span data-stu-id="6f462-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="6f462-509">Yapışkan oturumlar, bir istemciden gelen isteklerin işlenmek üzere her zaman aynı sunucuya yönlendirildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6f462-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="6f462-510">Daha fazla bilgi için bkz. <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="6f462-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="6f462-511">Dağıtılmış Önbellek</span><span class="sxs-lookup"><span data-stu-id="6f462-511">Distributed Cache</span></span>

<span data-ttu-id="6f462-512">Uygulama bir bulutta veya sunucu grubunda barındırıldığı zaman bellekte verileri depolamak için dağıtılmış önbellek kullanın.</span><span class="sxs-lookup"><span data-stu-id="6f462-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="6f462-513">Önbellek, istekleri işleyen sunucular arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="6f462-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="6f462-514">İstemci, önbelleğe alınmış veriler varsa, gruptaki herhangi bir sunucu tarafından işlenen bir istek gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="6f462-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="6f462-515">ASP.NET Core, SQL Server, [redsıs](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)ve [nCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) dağıtılmış önbellekler ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="6f462-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="6f462-516">Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="6f462-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="6f462-517">Önbellek Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="6f462-517">Cache Tag Helper</span></span>

<span data-ttu-id="6f462-518">İçeriği bir MVC görünümü veya Razor sayfasından önbellek etiketi Yardımcısı ile önbelleğe alma.</span><span class="sxs-lookup"><span data-stu-id="6f462-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="6f462-519">Önbellek etiketi Yardımcısı, verileri depolamak için bellek içi önbelleğe alma kullanır.</span><span class="sxs-lookup"><span data-stu-id="6f462-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="6f462-520">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="6f462-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="6f462-521">Dağıtılmış Önbellek Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="6f462-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="6f462-522">Dağıtılmış Razor önbellek etiketi Yardımcısı ile dağıtılmış bulut veya Web grubu senaryolarında BIR MVC görünümü veya sayfasından içerik önbelleğe alma.</span><span class="sxs-lookup"><span data-stu-id="6f462-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="6f462-523">Dağıtılmış önbellek etiketi Yardımcısı, verileri depolamak için SQL Server, [redsıs](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)veya [nCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) kullanır.</span><span class="sxs-lookup"><span data-stu-id="6f462-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="6f462-524">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="6f462-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="6f462-525">ResponseCache özniteliği</span><span class="sxs-lookup"><span data-stu-id="6f462-525">ResponseCache attribute</span></span>

<span data-ttu-id="6f462-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Yanıt önbelleğe alma işleminde uygun üst bilgileri ayarlamak için gereken parametreleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="6f462-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="6f462-527">Kimliği doğrulanmış istemciler için bilgi içeren içerik için önbelleğe almayı devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="6f462-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="6f462-528">Önbelleğe alma yalnızca kullanıcının kimliğine göre değişmeyen içerik veya bir kullanıcının oturum açmış olup olmadığı için etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6f462-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="6f462-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>saklı yanıtı, belirtilen sorgu anahtarları listesinin değerlerine göre değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6f462-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="6f462-530">Tek bir değer `*` sağlandığında, ara yazılım tüm istek sorgu dizesi parametrelerine göre yanıtları değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6f462-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="6f462-531">Özelliği ayarlamak için [yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) etkinleştirilmelidir <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="6f462-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="6f462-532">Aksi takdirde, çalışma zamanı özel durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6f462-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="6f462-533">Özellik için karşılık gelen bir HTTP üst bilgisi yok <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="6f462-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="6f462-534">Özelliği, yanıt önbelleğe alma ara yazılımı tarafından işlenen bir HTTP özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="6f462-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="6f462-535">Ara yazılım, önbelleğe alınmış bir yanıta hizmeti sağlamak için sorgu dizesi ve sorgu dizesi değerinin önceki bir istekle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6f462-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="6f462-536">Örneğin, aşağıdaki tabloda gösterilen istek sırasını ve sonuçları göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="6f462-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="6f462-537">İstek</span><span class="sxs-lookup"><span data-stu-id="6f462-537">Request</span></span>                          | <span data-ttu-id="6f462-538">Sonuç</span><span class="sxs-lookup"><span data-stu-id="6f462-538">Result</span></span>                    |
| ---
<span data-ttu-id="6f462-539">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-540">'Blazor'</span></span>
- <span data-ttu-id="6f462-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-541">'Identity'</span></span>
- <span data-ttu-id="6f462-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-543">'Razor'</span></span>
- <span data-ttu-id="6f462-544">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-545">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-546">'Blazor'</span></span>
- <span data-ttu-id="6f462-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-547">'Identity'</span></span>
- <span data-ttu-id="6f462-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-549">'Razor'</span></span>
- <span data-ttu-id="6f462-550">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-551">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-552">'Blazor'</span></span>
- <span data-ttu-id="6f462-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-553">'Identity'</span></span>
- <span data-ttu-id="6f462-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-555">'Razor'</span></span>
- <span data-ttu-id="6f462-556">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-557">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-558">'Blazor'</span></span>
- <span data-ttu-id="6f462-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-559">'Identity'</span></span>
- <span data-ttu-id="6f462-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-561">'Razor'</span></span>
- <span data-ttu-id="6f462-562">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-563">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-564">'Blazor'</span></span>
- <span data-ttu-id="6f462-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-565">'Identity'</span></span>
- <span data-ttu-id="6f462-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-567">'Razor'</span></span>
- <span data-ttu-id="6f462-568">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-569">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-570">'Blazor'</span></span>
- <span data-ttu-id="6f462-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-571">'Identity'</span></span>
- <span data-ttu-id="6f462-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-573">'Razor'</span></span>
- <span data-ttu-id="6f462-574">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-575">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-576">'Blazor'</span></span>
- <span data-ttu-id="6f462-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-577">'Identity'</span></span>
- <span data-ttu-id="6f462-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-579">'Razor'</span></span>
- <span data-ttu-id="6f462-580">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-581">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-582">'Blazor'</span></span>
- <span data-ttu-id="6f462-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-583">'Identity'</span></span>
- <span data-ttu-id="6f462-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-585">'Razor'</span></span>
- <span data-ttu-id="6f462-586">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-587">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-588">'Blazor'</span></span>
- <span data-ttu-id="6f462-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-589">'Identity'</span></span>
- <span data-ttu-id="6f462-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-591">'Razor'</span></span>
- <span data-ttu-id="6f462-592">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-593">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-594">'Blazor'</span></span>
- <span data-ttu-id="6f462-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-595">'Identity'</span></span>
- <span data-ttu-id="6f462-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-597">'Razor'</span></span>
- <span data-ttu-id="6f462-598">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-599">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-600">'Blazor'</span></span>
- <span data-ttu-id="6f462-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-601">'Identity'</span></span>
- <span data-ttu-id="6f462-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-603">'Razor'</span></span>
- <span data-ttu-id="6f462-604">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-605">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-606">'Blazor'</span></span>
- <span data-ttu-id="6f462-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-607">'Identity'</span></span>
- <span data-ttu-id="6f462-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-609">'Razor'</span></span>
- <span data-ttu-id="6f462-610">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-611">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-612">'Blazor'</span></span>
- <span data-ttu-id="6f462-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-613">'Identity'</span></span>
- <span data-ttu-id="6f462-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-615">'Razor'</span></span>
- <span data-ttu-id="6f462-616">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-617">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-618">'Blazor'</span></span>
- <span data-ttu-id="6f462-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-619">'Identity'</span></span>
- <span data-ttu-id="6f462-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-621">'Razor'</span></span>
- <span data-ttu-id="6f462-622">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-622">'SignalR' uid:</span></span> 

<span data-ttu-id="6f462-623">---------------- | ---başlığı: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-624">'Blazor'</span></span>
- <span data-ttu-id="6f462-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-625">'Identity'</span></span>
- <span data-ttu-id="6f462-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-627">'Razor'</span></span>
- <span data-ttu-id="6f462-628">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-629">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-630">'Blazor'</span></span>
- <span data-ttu-id="6f462-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-631">'Identity'</span></span>
- <span data-ttu-id="6f462-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-633">'Razor'</span></span>
- <span data-ttu-id="6f462-634">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-635">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-636">'Blazor'</span></span>
- <span data-ttu-id="6f462-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-637">'Identity'</span></span>
- <span data-ttu-id="6f462-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-639">'Razor'</span></span>
- <span data-ttu-id="6f462-640">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-641">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-642">'Blazor'</span></span>
- <span data-ttu-id="6f462-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-643">'Identity'</span></span>
- <span data-ttu-id="6f462-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-645">'Razor'</span></span>
- <span data-ttu-id="6f462-646">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-647">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-648">'Blazor'</span></span>
- <span data-ttu-id="6f462-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-649">'Identity'</span></span>
- <span data-ttu-id="6f462-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-651">'Razor'</span></span>
- <span data-ttu-id="6f462-652">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-653">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-654">'Blazor'</span></span>
- <span data-ttu-id="6f462-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-655">'Identity'</span></span>
- <span data-ttu-id="6f462-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-657">'Razor'</span></span>
- <span data-ttu-id="6f462-658">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-659">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-660">'Blazor'</span></span>
- <span data-ttu-id="6f462-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-661">'Identity'</span></span>
- <span data-ttu-id="6f462-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-663">'Razor'</span></span>
- <span data-ttu-id="6f462-664">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-665">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-666">'Blazor'</span></span>
- <span data-ttu-id="6f462-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-667">'Identity'</span></span>
- <span data-ttu-id="6f462-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-669">'Razor'</span></span>
- <span data-ttu-id="6f462-670">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-671">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-672">'Blazor'</span></span>
- <span data-ttu-id="6f462-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-673">'Identity'</span></span>
- <span data-ttu-id="6f462-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-675">'Razor'</span></span>
- <span data-ttu-id="6f462-676">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6f462-677">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6f462-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f462-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f462-678">'Blazor'</span></span>
- <span data-ttu-id="6f462-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f462-679">'Identity'</span></span>
- <span data-ttu-id="6f462-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f462-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f462-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f462-681">'Razor'</span></span>
- <span data-ttu-id="6f462-682">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6f462-682">'SignalR' uid:</span></span> 

<span data-ttu-id="6f462-683">------------- | | `http://example.com?key1=value1` | Sunucudan döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="6f462-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="6f462-684">| | `http://example.com?key1=value1` | Ara yazılım tarafından döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="6f462-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="6f462-685">| | `http://example.com?key1=value2` | Sunucudan döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="6f462-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="6f462-686">İlk istek sunucu tarafından döndürülür ve ara yazılım içinde önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="6f462-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="6f462-687">Sorgu dizesi önceki istekle eşleştiğinden, ikinci istek ara yazılım tarafından döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6f462-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="6f462-688">Sorgu dizesi değeri önceki bir istekle eşleşmediğinden, üçüncü istek ara yazılım önbelleğinde değil.</span><span class="sxs-lookup"><span data-stu-id="6f462-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="6f462-689">, <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> (Aracılığıyla) yapılandırmak ve oluşturmak için kullanılır <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="6f462-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="6f462-690">, `ResponseCacheFilter` Yanıtın uygun HTTP üstbilgilerini ve özelliklerini güncelleştirme işini gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="6f462-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="6f462-691">Filtre:</span><span class="sxs-lookup"><span data-stu-id="6f462-691">The filter:</span></span>

* <span data-ttu-id="6f462-692">, Ve için varolan tüm üstbilgileri kaldırır `Vary` `Cache-Control` `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="6f462-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="6f462-693">İçinde ayarlanan özelliklere göre uygun üstbilgileri yazar <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="6f462-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="6f462-694">Ayarlanırsa, yanıt önbelleğe alma HTTP özelliğini güncelleştirir <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="6f462-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="6f462-695">Değiş</span><span class="sxs-lookup"><span data-stu-id="6f462-695">Vary</span></span>

<span data-ttu-id="6f462-696">Bu üst bilgi yalnızca <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> özellik ayarlandığında yazılır.</span><span class="sxs-lookup"><span data-stu-id="6f462-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="6f462-697">Özelliği, `Vary` özelliğin değerine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6f462-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="6f462-698">Aşağıdaki örnek <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> özelliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="6f462-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="6f462-699">Örnek uygulamayı kullanarak, tarayıcının ağ araçlarıyla yanıt üst bilgilerini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="6f462-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="6f462-700">Aşağıdaki yanıt üst bilgileri Cache1 sayfa yanıtıyla gönderilir:</span><span class="sxs-lookup"><span data-stu-id="6f462-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="6f462-701">NoStore ve Location. None</span><span class="sxs-lookup"><span data-stu-id="6f462-701">NoStore and Location.None</span></span>

<span data-ttu-id="6f462-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>Diğer özelliklerin çoğunu geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="6f462-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="6f462-703">Bu özellik olarak ayarlandığında `true` , `Cache-Control` üst bilgi olarak ayarlanır `no-store` .</span><span class="sxs-lookup"><span data-stu-id="6f462-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="6f462-704"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>Şu şekilde ayarlanmışsa `None` :</span><span class="sxs-lookup"><span data-stu-id="6f462-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="6f462-705">`Cache-Control`, olarak ayarlanır `no-store,no-cache` .</span><span class="sxs-lookup"><span data-stu-id="6f462-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="6f462-706">`Pragma`, olarak ayarlanır `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="6f462-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="6f462-707">, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> Ve ise ve ise, `false` ve olarak <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None` `Cache-Control` `Pragma` ayarlanır `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="6f462-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="6f462-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>genellikle `true` hata sayfaları için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6f462-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="6f462-709">Örnek uygulamadaki Cache2 sayfası, istemcinin yanıtı depolayamamasını sağlayan yanıt üst bilgileri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6f462-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="6f462-710">Örnek uygulama, aşağıdaki üst bilgileri içeren Cache2 sayfasını döndürür:</span><span class="sxs-lookup"><span data-stu-id="6f462-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="6f462-711">Konum ve süre</span><span class="sxs-lookup"><span data-stu-id="6f462-711">Location and Duration</span></span>

<span data-ttu-id="6f462-712">Önbelleğe almayı etkinleştirmek için <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> pozitif bir değere ayarlanmalıdır ve <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `Any` (varsayılan) ya da olmalıdır `Client` .</span><span class="sxs-lookup"><span data-stu-id="6f462-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="6f462-713">Çerçeve, `Cache-Control` üst bilgisini konum değerine ve ardından `max-age` yanıtın sonuna ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6f462-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="6f462-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>öğesinin seçenekleri `Any` ve `Client` `Cache-Control` sırasıyla üst bilgi değerlerine dönüştürülür `public` `private` .</span><span class="sxs-lookup"><span data-stu-id="6f462-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="6f462-715">[NoStore ve Location. None](#nostore-and-locationnone) bölümünde belirtildiği gibi, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None` hem hem de `Cache-Control` `Pragma` üst bilgileri olarak ayarlar `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="6f462-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="6f462-716">`Location.Any`( `Cache-Control` olarak ayarlanır `public` ), *istemci veya herhangi bir ara proxy* 'Nin, [yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware)dahil olmak üzere değeri önbellekte olabileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6f462-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="6f462-717">`Location.Client`( `Cache-Control` olarak ayarlanır `private` ) *yalnızca istemcinin* değeri önbelleğe alabilir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="6f462-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="6f462-718">[Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware)da dahil olmak üzere, bir ara önbelleğin değeri önbelleğe almalıdır.</span><span class="sxs-lookup"><span data-stu-id="6f462-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="6f462-719">Önbellek denetim üstbilgileri, yalnızca istemcilere ve yanıt önbelleklerine ne zaman ve nasıl önbellek alınacağını gösteren yönergeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="6f462-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="6f462-720">İstemcilerin ve proxy 'lerin [HTTP 1,1 önbelleğe alma belirtimini](https://tools.ietf.org/html/rfc7234)kabul edeceğini garanti etmez.</span><span class="sxs-lookup"><span data-stu-id="6f462-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="6f462-721">[Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) her zaman belirtim tarafından düzenlendiği önbelleğe alma kurallarını izler.</span><span class="sxs-lookup"><span data-stu-id="6f462-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="6f462-722">Aşağıdaki örnek, örnek uygulamadan Cache3 sayfa modelini ve <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> varsayılan değeri ayarlayarak ve bırakarak oluşturulan üstbilgileri gösterir <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> :</span><span class="sxs-lookup"><span data-stu-id="6f462-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="6f462-723">Örnek uygulama, aşağıdaki üst bilgiyle Cache3 sayfasını döndürür:</span><span class="sxs-lookup"><span data-stu-id="6f462-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="6f462-724">Önbellek profilleri</span><span class="sxs-lookup"><span data-stu-id="6f462-724">Cache profiles</span></span>

<span data-ttu-id="6f462-725">Birçok denetleyici eylem özniteliği üzerinde yanıt önbelleği ayarlarını çoğaltmak yerine, ' de MVC/sayfalar ayarlanırken, önbellek profilleri seçenek olarak yapılandırılabilir Razor `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f462-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f462-726">Başvurulan bir önbellek profilinde bulunan değerler, tarafından varsayılan olarak kullanılır <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> ve özniteliğinde belirtilen özellikler tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="6f462-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="6f462-727">Önbellek profili ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6f462-727">Set up a cache profile.</span></span> <span data-ttu-id="6f462-728">Aşağıdaki örnek, örnek uygulamanın bir 30 saniyelik önbellek profilini göstermektedir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6f462-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="6f462-729">Örnek uygulamanın Cache4 sayfa modeli `Default30` önbellek profiline başvurur:</span><span class="sxs-lookup"><span data-stu-id="6f462-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="6f462-730">, <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Öğesine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="6f462-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="6f462-731">Sayfa işleyicileri (sınıflar): öznitelikler işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="6f462-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="6f462-732">MVC denetleyicileri (sınıflar).</span><span class="sxs-lookup"><span data-stu-id="6f462-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="6f462-733">MVC eylemleri (Yöntemler): Yöntem düzeyi öznitelikler, sınıf düzeyi özniteliklerde belirtilen ayarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="6f462-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="6f462-734">Önbellek profili tarafından Cache4 sayfa yanıtına uygulanan sonuç üst bilgisi `Default30` :</span><span class="sxs-lookup"><span data-stu-id="6f462-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="6f462-735">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6f462-735">Additional resources</span></span>

* [<span data-ttu-id="6f462-736">Yanıtları önbellekte depolama</span><span class="sxs-lookup"><span data-stu-id="6f462-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="6f462-737">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="6f462-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
