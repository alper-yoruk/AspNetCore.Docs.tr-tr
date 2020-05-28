---
<span data-ttu-id="c2fe3-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-102">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-103">'Identity'</span></span>
- <span data-ttu-id="c2fe3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-105">'Razor'</span></span>
- <span data-ttu-id="c2fe3-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="c2fe3-107">gRPC hizmetlerini HTTP API’leriyle karşılaştırma</span><span class="sxs-lookup"><span data-stu-id="c2fe3-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="c2fe3-108">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c2fe3-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c2fe3-109">Bu makalede, [GRPC HIZMETLERININ](https://grpc.io/docs/guides/) JSON Ile HTTP API 'lerle (ASP.NET Core [Web API 'leri](xref:web-api/index)dahil) nasıl karşılaştırılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="c2fe3-110">Uygulamanız için bir API sağlamak için kullanılan teknoloji önemli bir seçimdir ve gRPC, HTTP API 'Lerine kıyasla benzersiz avantajlar sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="c2fe3-111">Bu makalede, gRPC 'nin güçlü ve zayıf yönleri ele alınmaktadır ve gRPC 'nin diğer teknolojiler üzerinden kullanılması için senaryolar önerilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="c2fe3-112">Üst düzey karşılaştırma</span><span class="sxs-lookup"><span data-stu-id="c2fe3-112">High-level comparison</span></span>

<span data-ttu-id="c2fe3-113">Aşağıdaki tabloda, gRPC ve HTTP API 'Leri arasında JSON ile yüksek düzeyde bir karşılaştırma sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="c2fe3-114">Özellik</span><span class="sxs-lookup"><span data-stu-id="c2fe3-114">Feature</span></span>          | <span data-ttu-id="c2fe3-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="c2fe3-115">gRPC</span></span>                                               | <span data-ttu-id="c2fe3-116">JSON ile HTTP API 'Leri</span><span class="sxs-lookup"><span data-stu-id="c2fe3-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="c2fe3-117">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-118">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-119">'Identity'</span></span>
- <span data-ttu-id="c2fe3-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-121">'Razor'</span></span>
- <span data-ttu-id="c2fe3-122">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-123">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-124">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-125">'Identity'</span></span>
- <span data-ttu-id="c2fe3-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-127">'Razor'</span></span>
- <span data-ttu-id="c2fe3-128">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-129">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-130">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-131">'Identity'</span></span>
- <span data-ttu-id="c2fe3-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-133">'Razor'</span></span>
- <span data-ttu-id="c2fe3-134">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-135">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-136">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-137">'Identity'</span></span>
- <span data-ttu-id="c2fe3-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-139">'Razor'</span></span>
- <span data-ttu-id="c2fe3-140">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-141">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-142">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-143">'Identity'</span></span>
- <span data-ttu-id="c2fe3-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-145">'Razor'</span></span>
- <span data-ttu-id="c2fe3-146">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-147">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-148">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-149">'Identity'</span></span>
- <span data-ttu-id="c2fe3-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-151">'Razor'</span></span>
- <span data-ttu-id="c2fe3-152">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-152">'SignalR' uid:</span></span> 

<span data-ttu-id="c2fe3-153">-------- | ---başlığı: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-154">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-155">'Identity'</span></span>
- <span data-ttu-id="c2fe3-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-157">'Razor'</span></span>
- <span data-ttu-id="c2fe3-158">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-159">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-160">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-161">'Identity'</span></span>
- <span data-ttu-id="c2fe3-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-163">'Razor'</span></span>
- <span data-ttu-id="c2fe3-164">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-165">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-166">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-167">'Identity'</span></span>
- <span data-ttu-id="c2fe3-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-169">'Razor'</span></span>
- <span data-ttu-id="c2fe3-170">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-171">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-172">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-173">'Identity'</span></span>
- <span data-ttu-id="c2fe3-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-175">'Razor'</span></span>
- <span data-ttu-id="c2fe3-176">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-177">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-178">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-179">'Identity'</span></span>
- <span data-ttu-id="c2fe3-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-181">'Razor'</span></span>
- <span data-ttu-id="c2fe3-182">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-183">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-184">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-185">'Identity'</span></span>
- <span data-ttu-id="c2fe3-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-187">'Razor'</span></span>
- <span data-ttu-id="c2fe3-188">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-189">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-190">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-191">'Identity'</span></span>
- <span data-ttu-id="c2fe3-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-193">'Razor'</span></span>
- <span data-ttu-id="c2fe3-194">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-195">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-196">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-197">'Identity'</span></span>
- <span data-ttu-id="c2fe3-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-199">'Razor'</span></span>
- <span data-ttu-id="c2fe3-200">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-201">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-202">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-203">'Identity'</span></span>
- <span data-ttu-id="c2fe3-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-205">'Razor'</span></span>
- <span data-ttu-id="c2fe3-206">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-207">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-208">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-209">'Identity'</span></span>
- <span data-ttu-id="c2fe3-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-211">'Razor'</span></span>
- <span data-ttu-id="c2fe3-212">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-213">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-214">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-215">'Identity'</span></span>
- <span data-ttu-id="c2fe3-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-217">'Razor'</span></span>
- <span data-ttu-id="c2fe3-218">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-219">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-220">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-221">'Identity'</span></span>
- <span data-ttu-id="c2fe3-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-223">'Razor'</span></span>
- <span data-ttu-id="c2fe3-224">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-225">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-226">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-227">'Identity'</span></span>
- <span data-ttu-id="c2fe3-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-229">'Razor'</span></span>
- <span data-ttu-id="c2fe3-230">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-231">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-232">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-233">'Identity'</span></span>
- <span data-ttu-id="c2fe3-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-235">'Razor'</span></span>
- <span data-ttu-id="c2fe3-236">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-237">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-238">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-239">'Identity'</span></span>
- <span data-ttu-id="c2fe3-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-241">'Razor'</span></span>
- <span data-ttu-id="c2fe3-242">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-243">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-244">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-245">'Identity'</span></span>
- <span data-ttu-id="c2fe3-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-247">'Razor'</span></span>
- <span data-ttu-id="c2fe3-248">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-249">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-250">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-251">'Identity'</span></span>
- <span data-ttu-id="c2fe3-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-253">'Razor'</span></span>
- <span data-ttu-id="c2fe3-254">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-255">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-256">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-257">'Identity'</span></span>
- <span data-ttu-id="c2fe3-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-259">'Razor'</span></span>
- <span data-ttu-id="c2fe3-260">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-261">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-262">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-263">'Identity'</span></span>
- <span data-ttu-id="c2fe3-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-265">'Razor'</span></span>
- <span data-ttu-id="c2fe3-266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-267">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-268">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-269">'Identity'</span></span>
- <span data-ttu-id="c2fe3-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-271">'Razor'</span></span>
- <span data-ttu-id="c2fe3-272">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-273">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-274">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-275">'Identity'</span></span>
- <span data-ttu-id="c2fe3-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-277">'Razor'</span></span>
- <span data-ttu-id="c2fe3-278">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-279">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-280">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-281">'Identity'</span></span>
- <span data-ttu-id="c2fe3-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-283">'Razor'</span></span>
- <span data-ttu-id="c2fe3-284">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-285">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-286">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-287">'Identity'</span></span>
- <span data-ttu-id="c2fe3-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-289">'Razor'</span></span>
- <span data-ttu-id="c2fe3-290">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-290">'SignalR' uid:</span></span> 

<span data-ttu-id="c2fe3-291">------------------------- | ---başlığı: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-292">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-293">'Identity'</span></span>
- <span data-ttu-id="c2fe3-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-295">'Razor'</span></span>
- <span data-ttu-id="c2fe3-296">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-297">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-298">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-299">'Identity'</span></span>
- <span data-ttu-id="c2fe3-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-301">'Razor'</span></span>
- <span data-ttu-id="c2fe3-302">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-303">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-304">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-305">'Identity'</span></span>
- <span data-ttu-id="c2fe3-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-307">'Razor'</span></span>
- <span data-ttu-id="c2fe3-308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-309">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-310">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-311">'Identity'</span></span>
- <span data-ttu-id="c2fe3-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-313">'Razor'</span></span>
- <span data-ttu-id="c2fe3-314">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-315">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-316">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-317">'Identity'</span></span>
- <span data-ttu-id="c2fe3-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-319">'Razor'</span></span>
- <span data-ttu-id="c2fe3-320">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-321">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-322">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-323">'Identity'</span></span>
- <span data-ttu-id="c2fe3-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-325">'Razor'</span></span>
- <span data-ttu-id="c2fe3-326">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-327">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-328">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-329">'Identity'</span></span>
- <span data-ttu-id="c2fe3-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-331">'Razor'</span></span>
- <span data-ttu-id="c2fe3-332">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-333">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-334">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-335">'Identity'</span></span>
- <span data-ttu-id="c2fe3-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-337">'Razor'</span></span>
- <span data-ttu-id="c2fe3-338">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-339">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-340">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-341">'Identity'</span></span>
- <span data-ttu-id="c2fe3-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-343">'Razor'</span></span>
- <span data-ttu-id="c2fe3-344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-346">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-347">'Identity'</span></span>
- <span data-ttu-id="c2fe3-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-349">'Razor'</span></span>
- <span data-ttu-id="c2fe3-350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-352">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-353">'Identity'</span></span>
- <span data-ttu-id="c2fe3-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-355">'Razor'</span></span>
- <span data-ttu-id="c2fe3-356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2fe3-357">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2fe3-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-358">'Blazor'</span></span>
- <span data-ttu-id="c2fe3-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-359">'Identity'</span></span>
- <span data-ttu-id="c2fe3-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2fe3-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2fe3-361">'Razor'</span></span>
- <span data-ttu-id="c2fe3-362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-362">'SignalR' uid:</span></span> 

<span data-ttu-id="c2fe3-363">--------------- | | Sözleşme | Gerekli (*. proto*) | İsteğe bağlı (Openapı) | | Protokol | HTTP/2 | HTTP | | Yük | [Prototip (küçük, ikili)](#performance) | JSON (büyük, insan okunabilir) | | Öngörülebilirlik | [Katı belirtim](#strict-specification) | Miş.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="c2fe3-364">Herhangi bir HTTP geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-364">Any HTTP is valid.</span></span>     <span data-ttu-id="c2fe3-365">| | Akış | [İstemci, sunucu, iki yönlü](#streaming) | İstemci, sunucu | | Tarayıcı desteği | [Hayır (GRPC gerektirir-Web)](#limited-browser-support) | Evet | | Güvenlik | Taşıma (TLS) | Taşıma (TLS) | | İstemci kod üretimi | [Evet](#code-generation) | Openapı + üçüncü taraf araçları |</span><span class="sxs-lookup"><span data-stu-id="c2fe3-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="c2fe3-366">gRPC güçleri</span><span class="sxs-lookup"><span data-stu-id="c2fe3-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="c2fe3-367">Performans</span><span class="sxs-lookup"><span data-stu-id="c2fe3-367">Performance</span></span>

<span data-ttu-id="c2fe3-368">gRPC iletileri, etkin bir ikili ileti biçimi olan [Protoarabelleğe](https://developers.google.com/protocol-buffers/docs/overview)kullanılarak serileştirilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="c2fe3-369">Sunucu ve istemcide prototip çok hızlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="c2fe3-370">Prototip serileştirme, mobil uygulamalar gibi sınırlı bant genişliği senaryolarında önemli olan küçük ileti yüklerine neden olur.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="c2fe3-371">gRPC, http 1. x üzerinden önemli performans avantajları sağlayan büyük bir HTTP düzeltmesi olan HTTP/2 için tasarlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="c2fe3-372">İkili çerçeveleme ve sıkıştırma.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-372">Binary framing and compression.</span></span> <span data-ttu-id="c2fe3-373">HTTP/2 Protokolü, hem gönderme hem de alma sırasında kompakt ve verimlidir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="c2fe3-374">Tek bir TCP bağlantısı üzerinden birden çok HTTP/2 çağrısının çoğullama.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="c2fe3-375">Çoğullama [, satır başı engellemeyi](https://en.wikipedia.org/wiki/Head-of-line_blocking)ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="c2fe3-376">HTTP/2, gRPC 'ye özel değildir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="c2fe3-377">JSON ile HTTP API 'Leri de dahil olmak üzere birçok istek türü, HTTP/2 kullanabilir ve performans geliştirmelerinden faydalanabilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="c2fe3-378">Kod oluşturma</span><span class="sxs-lookup"><span data-stu-id="c2fe3-378">Code generation</span></span>

<span data-ttu-id="c2fe3-379">Tüm gRPC çerçeveleri, kod oluşturma için birinci sınıf destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="c2fe3-380">GRPC geliştirmeye yönelik bir çekirdek dosya, gRPC Hizmetleri ve iletilerinin sözleşmesini tanımlayan [. proto dosyasıdır](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="c2fe3-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="c2fe3-381">Bu dosya gRPC çerçevelerinden kod, bir hizmet temel sınıfı, iletiler ve tüm istemci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="c2fe3-382">Sunucu ile istemci arasında *. proto* dosyasını paylaşarak iletiler ve istemci kodu uçtan uca oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="c2fe3-383">İstemcinin kod üretimi, istemci ve sunucudaki iletilerin çoğaltılmasını ortadan kaldırır ve sizin için kesin olarak belirlenmiş bir istemci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="c2fe3-384">İstemci yazmak gerekmez, birçok hizmet içeren uygulamalarda önemli geliştirme süresini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="c2fe3-385">Katı belirtim</span><span class="sxs-lookup"><span data-stu-id="c2fe3-385">Strict specification</span></span>

<span data-ttu-id="c2fe3-386">JSON ile HTTP API 'SI için biçimsel belirtim yok.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="c2fe3-387">Geliştiriciler, URL 'Lerin, HTTP fiillerinin ve yanıt kodlarının en iyi biçimini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="c2fe3-388">[GRPC belirtimi](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) , bir GRPC hizmetinin izlenmesi gereken biçim hakkında ayrıntılı bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="c2fe3-389">GRPC, platformlar ve uygulamalar arasında tutarlı olduğundan, GRPC başarılacağı 'yı ortadan kaldırır ve geliştirici süresini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="c2fe3-390">Akış</span><span class="sxs-lookup"><span data-stu-id="c2fe3-390">Streaming</span></span>

<span data-ttu-id="c2fe3-391">HTTP/2, uzun süreli, gerçek zamanlı iletişim akışları için bir temel sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="c2fe3-392">gRPC, HTTP/2 üzerinden akış için birinci sınıf destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="c2fe3-393">GRPC hizmeti tüm akış kombinasyonlarını destekler:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="c2fe3-394">Birli (akış yok)</span><span class="sxs-lookup"><span data-stu-id="c2fe3-394">Unary (no streaming)</span></span>
* <span data-ttu-id="c2fe3-395">Sunucudan istemciye akışa</span><span class="sxs-lookup"><span data-stu-id="c2fe3-395">Server to client streaming</span></span>
* <span data-ttu-id="c2fe3-396">İstemciden sunucuya akışa</span><span class="sxs-lookup"><span data-stu-id="c2fe3-396">Client to server streaming</span></span>
* <span data-ttu-id="c2fe3-397">İki yönlü akış</span><span class="sxs-lookup"><span data-stu-id="c2fe3-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="c2fe3-398">Son tarih/zaman aşımları ve iptal</span><span class="sxs-lookup"><span data-stu-id="c2fe3-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="c2fe3-399">gRPC, istemcilerin bir RPC 'nin tamamlanmasını beklemek zorunda kalabilecekleri süreyi belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="c2fe3-400">[Son tarih](https://grpc.io/blog/deadlines) sunucuya gönderilir ve sunucu son tarihi aşarsa hangi eylemin yapılacağını seçebilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="c2fe3-401">Örneğin, sunucu devam eden gRPC/HTTP/veritabanı isteklerini zaman aşımında iptal edebilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="c2fe3-402">Son tarihi ve iptali alt gRPC çağrıları aracılığıyla yayın, kaynak kullanım sınırlarının uygulanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="c2fe3-403">gRPC önerilen senaryolar</span><span class="sxs-lookup"><span data-stu-id="c2fe3-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="c2fe3-404">gRPC aşağıdaki senaryolara uygundur:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="c2fe3-405">**Mikro hizmetler**: GRPC, düşük gecikme süresi ve yüksek işleme iletişimi için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="c2fe3-406">gRPC, verimlilik açısından kritik olan hafif mikro hizmetler için harika.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="c2fe3-407">**Noktadan noktaya gerçek zamanlı iletişim**: GRPC, iki yönlü akış için harika desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="c2fe3-408">gRPC Hizmetleri, yoklama yapmadan iletileri gerçek zamanlı olarak gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="c2fe3-409">**Çok yönlü ortamları**: GRPC araçları, tüm popüler geliştirme dillerini destekler ve bu da GRPC 'yi çok dilli ortamlar için iyi bir seçenek yapar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="c2fe3-410">**Ağ kısıtlamalı ortamlar**: GRPC iletileri, hafif bir ileti biçimi olan protoarabellek ile serileştirilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="c2fe3-411">GRPC iletisi her zaman denk bir JSON iletisinden daha küçüktür.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="c2fe3-412">gRPC zayıflığı</span><span class="sxs-lookup"><span data-stu-id="c2fe3-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="c2fe3-413">Sınırlı tarayıcı desteği</span><span class="sxs-lookup"><span data-stu-id="c2fe3-413">Limited browser support</span></span>

<span data-ttu-id="c2fe3-414">Doğrudan bir tarayıcıda bir gRPC hizmetini doğrudan çağırmak olanaksızdır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="c2fe3-415">gRPC, HTTP/2 özelliklerini çok fazla kullanır ve tarayıcı, bir gRPC istemcisini desteklemek için Web istekleri üzerinde gerekli denetim düzeyini sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="c2fe3-416">Örneğin, tarayıcılar arayan HTTP/2 ' nin kullanılmasına izin vermez veya temel alınan HTTP/2 çerçevelerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="c2fe3-417">[GRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) , GRPC ekibinin tarayıcıda sınırlı GRPC desteği sağlayan ek bir teknolojidir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="c2fe3-418">gRPC-Web iki bölümden oluşur: tüm modern tarayıcıları ve sunucudaki gRPC-Web proxy 'sini destekleyen bir JavaScript istemcisi.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="c2fe3-419">GRPC-Web istemcisi ara sunucuyu çağırır ve proxy, gRPC isteklerini gRPC sunucusuna iletir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="c2fe3-420">GRPC 'nin özelliklerinin hepsi gRPC-Web tarafından desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="c2fe3-421">İstemci ve iki yönlü akış desteklenmez ve sunucu akışı için sınırlı destek vardır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="c2fe3-422">.NET Core 'da gRPC-Web için deneysel destek vardır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="c2fe3-423"><xref:grpc/browser>Daha fazla bilgi için ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="c2fe3-424">Okunabilir değil</span><span class="sxs-lookup"><span data-stu-id="c2fe3-424">Not human readable</span></span>

<span data-ttu-id="c2fe3-425">HTTP API istekleri metin olarak gönderilir ve insanların okuyabilmesi ve oluşturulabilmesi olabilir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="c2fe3-426">gRPC iletileri varsayılan olarak Protodeğer ile kodlanır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="c2fe3-427">Protoarabellek gönderme ve alma açısından verimli olsa da, ikili biçimi insanlar tarafından okunabilir değildir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="c2fe3-428">Protoarabelleğe doğru bir şekilde seri durumdan çıkarmak için *. proto* dosyasında belirtilen iletinin arabirim açıklaması gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="c2fe3-429">Ek araçlar, hat üzerindeki prototiplerin yüklerini analiz etmek ve istekleri el ile oluşturmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="c2fe3-430">[Sunucu yansıtma](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) ve [GRPC komut satırı aracı](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) gibi özellikler, ikili prototipsiz iletilerle yardım etmek için mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="c2fe3-431">Ayrıca, prototipli mesajlar [JSON öğesine ve öğesinden dönüştürmeyi](https://developers.google.com/protocol-buffers/docs/proto3#json)destekler.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="c2fe3-432">Yerleşik JSON dönüştürmesi, hata ayıklarken prototip iletileri okunabilir ve okunabilir biçime dönüştürmek için etkili bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="c2fe3-433">Alternatif Framework senaryoları</span><span class="sxs-lookup"><span data-stu-id="c2fe3-433">Alternative framework scenarios</span></span>

<span data-ttu-id="c2fe3-434">Aşağıdaki senaryolarda gRPC üzerinden diğer çerçeveler önerilir:</span><span class="sxs-lookup"><span data-stu-id="c2fe3-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="c2fe3-435">**Tarayıcıda erişilebilen API 'ler**: GRPC tarayıcıda tam olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="c2fe3-436">gRPC-Web tarayıcı desteği sunabilir, ancak sınırlamaları vardır ve sunucu proxy 'sini tanıtır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="c2fe3-437">**Gerçek zamanlı Iletişim yayınlama**: GRPC akış aracılığıyla gerçek zamanlı iletişimi destekler, ancak kayıtlı bağlantılara bir ileti yayınlama kavramı mevcut değildir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="c2fe3-438">Örneğin, sohbet odasındaki tüm istemcilere yeni sohbet iletilerinin gönderilmesi gereken bir sohbet odası senaryosunda her bir gRPC çağrısı, istemciye yeni sohbet iletilerini tek tek akışa almak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="c2fe3-439">[SignalR](xref:signalr/introduction)Bu senaryo için kullanışlı bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="c2fe3-440">, sürekli bağlantılar ve yayın iletileri için yerleşik destek kavramıdır.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="c2fe3-441">İşlemler **arası iletişim**: bir işlemin, gelen GRPC çağrılarını kabul etmek IÇIN bir http/2 sunucusunu barındırması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="c2fe3-442">Windows için, işlemler arası iletişim [kanalları](/dotnet/standard/io/pipe-operations) hızlı ve hafif bir iletişim yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="c2fe3-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2fe3-443">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c2fe3-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
