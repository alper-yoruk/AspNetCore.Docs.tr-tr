---
title: HTTP REPL telemetrisi
author: scottaddie
description: HTTP REPL tarafından toplanan telemetri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502044"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="9613d-103">HTTP REPL telemetrisi</span><span class="sxs-lookup"><span data-stu-id="9613d-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="9613d-104">[Http okuma-değerlendirme-yazdırma döngüsü (REPL)](xref:web-api/http-repl) , kullanım verilerini toplayan bir telemetri özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="9613d-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="9613d-105">HTTP REPL ekibinin, aracın daha iyi bir şekilde nasıl kullanıldığını anladığından emin olmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="9613d-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="9613d-106">Devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="9613d-106">How to opt out</span></span>

<span data-ttu-id="9613d-107">HTTP REPL telemetri özelliği varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9613d-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="9613d-108">Telemetri özelliğini devre dışı bırakmak için, `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` ortam değişkenini veya olarak ayarlayın `1` `true` .</span><span class="sxs-lookup"><span data-stu-id="9613d-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="9613d-109">Savunmasız</span><span class="sxs-lookup"><span data-stu-id="9613d-109">Disclosure</span></span>

<span data-ttu-id="9613d-110">HttpRepl, aracı ilk kez çalıştırdığınızda aşağıdakine benzer bir metin görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9613d-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="9613d-111">Metin, çalıştırdığınız aracın sürümüne bağlı olarak biraz farklılık gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="9613d-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="9613d-112">Bu "ilk çalıştırma" deneyimi, Microsoft 'un veri toplamayı nasıl bildisidir.</span><span class="sxs-lookup"><span data-stu-id="9613d-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="9613d-113">Veri noktaları</span><span class="sxs-lookup"><span data-stu-id="9613d-113">Data points</span></span>

<span data-ttu-id="9613d-114">Telemetri özelliği şunları yapmaz:</span><span class="sxs-lookup"><span data-stu-id="9613d-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="9613d-115">Kullanıcı adları, e-posta adresleri veya URL 'Ler gibi kişisel verileri toplayın.</span><span class="sxs-lookup"><span data-stu-id="9613d-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="9613d-116">HTTP isteklerinizi veya yanıtlarınızı tarayın.</span><span class="sxs-lookup"><span data-stu-id="9613d-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="9613d-117">Veriler, Microsoft sunucularına güvenli bir şekilde gönderilir ve kısıtlı erişim altında tutulur.</span><span class="sxs-lookup"><span data-stu-id="9613d-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="9613d-118">Gizliliğinizi korumak bizim için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="9613d-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="9613d-119">Telemetri özelliğinin hassas verileri toplamasını veya verilerin güvenli veya uygun şekilde işlenmekte olduğunu düşünüyorsanız, aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="9613d-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="9613d-120">[DotNet/httprepl](https://github.com/dotnet/httprepl/issues) deposunda bir sorun verin.</span><span class="sxs-lookup"><span data-stu-id="9613d-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="9613d-121">Araştırma için adresine bir e-posta gönderin [dotnet@microsoft.com](mailto:dotnet@microsoft.com) .</span><span class="sxs-lookup"><span data-stu-id="9613d-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="9613d-122">Telemetri özelliği aşağıdaki verileri toplar.</span><span class="sxs-lookup"><span data-stu-id="9613d-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="9613d-123">.NET SDK sürümleri</span><span class="sxs-lookup"><span data-stu-id="9613d-123">.NET SDK versions</span></span> | <span data-ttu-id="9613d-124">Veriler</span><span class="sxs-lookup"><span data-stu-id="9613d-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="9613d-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-125">>=5.0</span></span>        | <span data-ttu-id="9613d-126">Çağırma zaman damgası.</span><span class="sxs-lookup"><span data-stu-id="9613d-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="9613d-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-127">>=5.0</span></span>        | <span data-ttu-id="9613d-128">Coğrafi konumu belirlemede kullanılan üç sekizli IP adresi.</span><span class="sxs-lookup"><span data-stu-id="9613d-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="9613d-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-129">>=5.0</span></span>        | <span data-ttu-id="9613d-130">İşletim sistemi ve sürümü.</span><span class="sxs-lookup"><span data-stu-id="9613d-130">Operating system and version.</span></span> |
| <span data-ttu-id="9613d-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-131">>=5.0</span></span>        | <span data-ttu-id="9613d-132">Aracının üzerinde çalıştığı çalışma zamanı KIMLIĞI (RID).</span><span class="sxs-lookup"><span data-stu-id="9613d-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="9613d-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-133">>=5.0</span></span>        | <span data-ttu-id="9613d-134">Aracın bir kapsayıcıda çalışıp çalışmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="9613d-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="9613d-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-135">>=5.0</span></span>        | <span data-ttu-id="9613d-136">Karma medya Access Control (MAC) adresi: bir makine için bir şifreleme (SHA256) karma ve benzersiz KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="9613d-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="9613d-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-137">>=5.0</span></span>        | <span data-ttu-id="9613d-138">Çekirdek sürümü.</span><span class="sxs-lookup"><span data-stu-id="9613d-138">Kernel version.</span></span> |
| <span data-ttu-id="9613d-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-139">>=5.0</span></span>        | <span data-ttu-id="9613d-140">HTTP REPL sürümü.</span><span class="sxs-lookup"><span data-stu-id="9613d-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="9613d-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-141">>=5.0</span></span>        | <span data-ttu-id="9613d-142">Aracın `help` , `run` veya bağımsız değişkenlerle başlatılmış olup olmadığı `connect` .</span><span class="sxs-lookup"><span data-stu-id="9613d-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="9613d-143">Gerçek bağımsız değişken değerleri toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="9613d-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="9613d-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-144">>=5.0</span></span>        | <span data-ttu-id="9613d-145">Komut çağrıldı (örneğin, `get` ) ve başarılı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="9613d-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="9613d-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-146">>=5.0</span></span>        | <span data-ttu-id="9613d-147">Komut için,, `connect` `root` `base` veya `openapi` bağımsız değişkenlerin sağlanmış olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="9613d-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="9613d-148">Gerçek bağımsız değişken değerleri toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="9613d-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="9613d-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-149">>=5.0</span></span>        | <span data-ttu-id="9613d-150">Komut için `pref` , bir `get` veya `set` verilen ve hangi tercihe erişildiğine bakılmaksızın.</span><span class="sxs-lookup"><span data-stu-id="9613d-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="9613d-151">İyi bilinen bir tercih yoksa, ad karma hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="9613d-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="9613d-152">Değer toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="9613d-152">The value isn't collected.</span></span> |
| <span data-ttu-id="9613d-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-153">>=5.0</span></span>        | <span data-ttu-id="9613d-154">Komut için `set header` , ayarlanan üst bilgi adı.</span><span class="sxs-lookup"><span data-stu-id="9613d-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="9613d-155">İyi bilinen bir üst bilgi değilse, ad karma hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="9613d-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="9613d-156">Değer toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="9613d-156">The value isn't collected.</span></span> |
| <span data-ttu-id="9613d-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-157">>=5.0</span></span>        | <span data-ttu-id="9613d-158">Komutu için `connect` , için özel bir durumun `dotnet new webapi` ve tercih aracılığıyla atlanıp atlanmayacağı.</span><span class="sxs-lookup"><span data-stu-id="9613d-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="9613d-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="9613d-159">>=5.0</span></span>        | <span data-ttu-id="9613d-160">Tüm HTTP komutları için (örneğin, GET, POST, PUT), her bir seçeneğin belirtilmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="9613d-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="9613d-161">Seçeneklerin değerleri toplanmaz.</span><span class="sxs-lookup"><span data-stu-id="9613d-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="9613d-162">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9613d-162">Additional resources</span></span>

* [<span data-ttu-id="9613d-163">.NET Core SDK telemetri</span><span class="sxs-lookup"><span data-stu-id="9613d-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="9613d-164">Telemetri verileri .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9613d-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
