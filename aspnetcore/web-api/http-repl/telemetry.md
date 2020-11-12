---
title: Httprepın telemetrisi
author: scottaddie
description: HttpRepl tarafından toplanan telemetri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550614"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="b7d1d-103">Httprepın telemetrisi</span><span class="sxs-lookup"><span data-stu-id="b7d1d-103">HttpRepl telemetry</span></span>

<span data-ttu-id="b7d1d-104">[Httprepl](xref:web-api/http-repl) , kullanım verilerini toplayan bir telemetri özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="b7d1d-105">HttpRepl ekibinin, aracın nasıl kullanıldığını anlayabilmesi önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="b7d1d-106">Devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="b7d1d-106">How to opt out</span></span>

<span data-ttu-id="b7d1d-107">Bu varsayılan olarak HttpRepl telemetri özelliği etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="b7d1d-108">Telemetri özelliğini devre dışı bırakmak için, `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` ortam değişkenini veya olarak ayarlayın `1` `true` .</span><span class="sxs-lookup"><span data-stu-id="b7d1d-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="b7d1d-109">Savunmasız</span><span class="sxs-lookup"><span data-stu-id="b7d1d-109">Disclosure</span></span>

<span data-ttu-id="b7d1d-110">HttpRepl, aracı ilk kez çalıştırdığınızda aşağıdakine benzer bir metin görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="b7d1d-111">Metin, çalıştırdığınız aracın sürümüne bağlı olarak biraz farklılık gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="b7d1d-112">Bu "ilk çalıştırma" deneyimi, Microsoft 'un veri toplamayı nasıl bildisidir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="b7d1d-113">"İlk çalıştırma" deneyimi metnini bastırmak için, `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` ortam değişkenini veya olarak ayarlayın `1` `true` .</span><span class="sxs-lookup"><span data-stu-id="b7d1d-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="b7d1d-114">Veri noktaları</span><span class="sxs-lookup"><span data-stu-id="b7d1d-114">Data points</span></span>

<span data-ttu-id="b7d1d-115">Telemetri özelliği şunları yapmaz:</span><span class="sxs-lookup"><span data-stu-id="b7d1d-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="b7d1d-116">Kullanıcı adları, e-posta adresleri veya URL 'Ler gibi kişisel verileri toplayın.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="b7d1d-117">HTTP isteklerinizi veya yanıtlarınızı tarayın.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="b7d1d-118">Veriler, Microsoft sunucularına güvenli bir şekilde gönderilir ve kısıtlı erişim altında tutulur.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="b7d1d-119">Gizliliğinizi korumak bizim için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="b7d1d-120">Telemetri özelliğinin hassas verileri toplamasını veya verilerin güvenli veya uygun şekilde işlenmekte olduğunu düşünüyorsanız, aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="b7d1d-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="b7d1d-121">[DotNet/httprepl](https://github.com/dotnet/httprepl/issues) deposunda bir sorun verin.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="b7d1d-122">Araştırma için adresine bir e-posta gönderin [dotnet@microsoft.com](mailto:dotnet@microsoft.com) .</span><span class="sxs-lookup"><span data-stu-id="b7d1d-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="b7d1d-123">Telemetri özelliği aşağıdaki verileri toplar.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="b7d1d-124">.NET SDK sürümleri</span><span class="sxs-lookup"><span data-stu-id="b7d1d-124">.NET SDK versions</span></span> | <span data-ttu-id="b7d1d-125">Veriler</span><span class="sxs-lookup"><span data-stu-id="b7d1d-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="b7d1d-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-126">>=5.0</span></span>        | <span data-ttu-id="b7d1d-127">Çağırma zaman damgası.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="b7d1d-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-128">>=5.0</span></span>        | <span data-ttu-id="b7d1d-129">Coğrafi konumu belirlemede kullanılan üç sekizli IP adresi.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="b7d1d-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-130">>=5.0</span></span>        | <span data-ttu-id="b7d1d-131">İşletim sistemi ve sürümü.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-131">Operating system and version.</span></span> |
| <span data-ttu-id="b7d1d-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-132">>=5.0</span></span>        | <span data-ttu-id="b7d1d-133">Aracının üzerinde çalıştığı çalışma zamanı KIMLIĞI (RID).</span><span class="sxs-lookup"><span data-stu-id="b7d1d-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="b7d1d-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-134">>=5.0</span></span>        | <span data-ttu-id="b7d1d-135">Aracın bir kapsayıcıda çalışıp çalışmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="b7d1d-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-136">>=5.0</span></span>        | <span data-ttu-id="b7d1d-137">Karma medya Access Control (MAC) adresi: bir makine için bir şifreleme (SHA256) karma ve benzersiz KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="b7d1d-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-138">>=5.0</span></span>        | <span data-ttu-id="b7d1d-139">Çekirdek sürümü.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-139">Kernel version.</span></span> |
| <span data-ttu-id="b7d1d-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-140">>=5.0</span></span>        | <span data-ttu-id="b7d1d-141">HttpRepl sürümü.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-141">HttpRepl version.</span></span> |
| <span data-ttu-id="b7d1d-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-142">>=5.0</span></span>        | <span data-ttu-id="b7d1d-143">Aracın `help` , `run` veya bağımsız değişkenlerle başlatılmış olup olmadığı `connect` .</span><span class="sxs-lookup"><span data-stu-id="b7d1d-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="b7d1d-144">Gerçek bağımsız değişken değerleri toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="b7d1d-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-145">>=5.0</span></span>        | <span data-ttu-id="b7d1d-146">Komut çağrıldı (örneğin, `get` ) ve başarılı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="b7d1d-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-147">>=5.0</span></span>        | <span data-ttu-id="b7d1d-148">Komut için,, `connect` `root` `base` veya `openapi` bağımsız değişkenlerin sağlanmış olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="b7d1d-149">Gerçek bağımsız değişken değerleri toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="b7d1d-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-150">>=5.0</span></span>        | <span data-ttu-id="b7d1d-151">Komut için `pref` , bir `get` veya `set` verilen ve hangi tercihe erişildiğine bakılmaksızın.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="b7d1d-152">İyi bilinen bir tercih yoksa, ad karma hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="b7d1d-153">Değer toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-153">The value isn't collected.</span></span> |
| <span data-ttu-id="b7d1d-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-154">>=5.0</span></span>        | <span data-ttu-id="b7d1d-155">Komut için `set header` , ayarlanan üst bilgi adı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="b7d1d-156">İyi bilinen bir üst bilgi değilse, ad karma hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="b7d1d-157">Değer toplanmadı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-157">The value isn't collected.</span></span> |
| <span data-ttu-id="b7d1d-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-158">>=5.0</span></span>        | <span data-ttu-id="b7d1d-159">Komutu için `connect` , için özel bir durumun `dotnet new webapi` ve tercih aracılığıyla atlanıp atlanmayacağı.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="b7d1d-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="b7d1d-160">>=5.0</span></span>        | <span data-ttu-id="b7d1d-161">Tüm HTTP komutları için (örneğin, GET, POST, PUT), her bir seçeneğin belirtilmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="b7d1d-162">Seçeneklerin değerleri toplanmaz.</span><span class="sxs-lookup"><span data-stu-id="b7d1d-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="b7d1d-163">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b7d1d-163">Additional resources</span></span>

* [<span data-ttu-id="b7d1d-164">.NET Core SDK telemetri</span><span class="sxs-lookup"><span data-stu-id="b7d1d-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="b7d1d-165">Telemetri verileri .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7d1d-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
