---
title: ASP.NET Core korumalı yüklerin ömrünü sınırlayın
author: rick-anderson
description: ASP.NET Core veri koruma API 'Lerini kullanarak korumalı yükün ömrünü sınırlamayı öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: c7bc86cd42a725f21cf66187c033376a8c5a9e65
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014834"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="fbb6d-103">ASP.NET Core korumalı yüklerin ömrünü sınırlayın</span><span class="sxs-lookup"><span data-stu-id="fbb6d-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="fbb6d-104">Uygulama geliştiricisinin, belirlenen süre sonunda süresi dolan korumalı bir yük oluşturmak istediği senaryolar vardır.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="fbb6d-105">Örneğin, korumalı yük yalnızca bir saat için geçerli olması gereken bir parola sıfırlama belirtecini temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="fbb6d-106">Geliştiricinin, gömülü bir sona erme tarihi içeren kendi yük biçimini oluşturması kesinlikle olasıdır ve gelişmiş geliştiriciler bunu yapmak isteyebilir, ancak bu süre sonlarını yöneten geliştiricilerin çoğunluğu sıkıcı bir şekilde büyüyebilir.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="fbb6d-107">Geliştirici izleyicilerimizin bu kadar kolay olması için, [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) paketi, belirlenen süre sonunda otomatik olarak süresi dolan yük oluşturma API 'lerini içerir.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="fbb6d-108">Bu API 'Ler, türden kapanır `ITimeLimitedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="fbb6d-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="fbb6d-109">API kullanımı</span><span class="sxs-lookup"><span data-stu-id="fbb6d-109">API usage</span></span>

<span data-ttu-id="fbb6d-110">`ITimeLimitedDataProtector`Arabirim, zaman sınırlı/kendi kendine süresi dolan yükleri korumak ve korumayı kaldırmak için çekirdek arabirimdir.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="fbb6d-111">Bir örneğini oluşturmak için `ITimeLimitedDataProtector` öncelikle belirli bir amaca göre oluşturulan normal bir [ıdataprotector](xref:security/data-protection/consumer-apis/overview) örneğine ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="fbb6d-112">`IDataProtector`Örnek kullanılabilir olduğunda, `IDataProtector.ToTimeLimitedDataProtector` yerleşik süre sonu özelliklerine sahip bir koruyucu geri almak için genişletme yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="fbb6d-113">`ITimeLimitedDataProtector`Aşağıdaki API yüzeyini ve genişletme yöntemlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="fbb6d-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="fbb6d-114">CreateProtector (dize amacı): ıomelimiteddataprotector-bu API, `IDataProtectionProvider.CreateProtector` bir kök zaman sınırlı koruyucudan [Amaç zincirlerini](xref:security/data-protection/consumer-apis/purpose-strings) oluşturmak için kullanılabilecek mevcut öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="fbb6d-115">Koruma (Byte [] düz metin, DateTimeOffset süre sonu): Byte []</span><span class="sxs-lookup"><span data-stu-id="fbb6d-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="fbb6d-116">Koruma (Byte [] düz metin, TimeSpan yaşam süresi): Byte []</span><span class="sxs-lookup"><span data-stu-id="fbb6d-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="fbb6d-117">Koruma (Byte [] düz metin): Byte []</span><span class="sxs-lookup"><span data-stu-id="fbb6d-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="fbb6d-118">Koruma (dize düz metin, DateTimeOffset süre sonu): dize</span><span class="sxs-lookup"><span data-stu-id="fbb6d-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="fbb6d-119">Koruma (dize düz metin, zaman aralığı ömrü): dize</span><span class="sxs-lookup"><span data-stu-id="fbb6d-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="fbb6d-120">Koru (dize düz metin): dize</span><span class="sxs-lookup"><span data-stu-id="fbb6d-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="fbb6d-121">`Protect`Yalnızca düz metin olan temel yöntemlere ek olarak, yükün sona erme tarihinin belirtilmesine izin veren yeni aşırı yüklemeler vardır.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="fbb6d-122">Sona erme tarihi mutlak bir tarih (bir ile `DateTimeOffset` ) veya göreli bir süre (geçerli sistem saatinden bir ile) olarak belirtilebilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="fbb6d-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="fbb6d-123">Süre sonu olmayan bir aşırı yükleme çağrıldığında yükün süresinin dolmayacağı varsayılır.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="fbb6d-124">Korumasını kaldır (Byte [] protectedData, Out DateTimeOffset süre sonu): Byte []</span><span class="sxs-lookup"><span data-stu-id="fbb6d-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="fbb6d-125">Korumasını kaldır (Byte [] protectedData): Byte []</span><span class="sxs-lookup"><span data-stu-id="fbb6d-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="fbb6d-126">Korumasını kaldırma (dize protectedData, Out DateTimeOffset Expiration): dize</span><span class="sxs-lookup"><span data-stu-id="fbb6d-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="fbb6d-127">Korumasını kaldır (dize protectedData): dize</span><span class="sxs-lookup"><span data-stu-id="fbb6d-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="fbb6d-128">`Unprotect`Yöntemler, orijinal korumasız verileri döndürür.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="fbb6d-129">Yükün süresi dolmamışsa, mutlak süre sonu, özgün korumasız verilerle birlikte isteğe bağlı bir out parametresi olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="fbb6d-130">Yükün geçerliliği dolmuşsa, korumasını kaldırma yönteminin tüm aşırı yüklemeleri CryptographicException oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="fbb6d-131">Uzun süreli veya sonsuz Kalıcılık gerektiren yükleri korumak için bu API 'Lerin kullanılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="fbb6d-132">"Korumalı yüklerin bir aydan sonra kalıcı olarak kurtarılamadığında emin olabilir mi?"</span><span class="sxs-lookup"><span data-stu-id="fbb6d-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="fbb6d-133">, Thumb 'in iyi bir kuralı olarak işlev görebilir; Yanıt yoksa, geliştiricilerin alternatif API 'Leri dikkate almalıdır.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="fbb6d-134">Aşağıdaki örnek, veri koruma sisteminin örneğini oluşturmak için, [dı olmayan kod yollarını](xref:security/data-protection/configuration/non-di-scenarios) kullanır.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="fbb6d-135">Bu örneği çalıştırmak için, ilk olarak Microsoft. AspNetCore. DataProtection. Extensions paketine bir başvuru eklemiş olduğunuzdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="fbb6d-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
