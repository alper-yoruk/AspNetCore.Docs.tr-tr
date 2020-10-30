---
title: ASP.NET Core kısa ömürlü veri koruma sağlayıcıları
author: rick-anderson
description: ASP.NET Core kısa ömürlü veri koruma sağlayıcılarının uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052708"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="aa0d4-103">ASP.NET Core kısa ömürlü veri koruma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="aa0d4-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="aa0d4-104">Bir uygulamanın bir throwaway ihtiyacı olan senaryolar vardır `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="aa0d4-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="aa0d4-105">Örneğin, geliştirici yalnızca tek bir konsol uygulamasında çalışabilir ya da uygulamanın kendisi geçici (betikleştirilmiş veya bir birim test projesi).</span><span class="sxs-lookup"><span data-stu-id="aa0d4-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="aa0d4-106">Bu senaryoları desteklemek için, [Microsoft. AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) paketi bir tür içerir `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="aa0d4-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="aa0d4-107">Bu tür, `IDataProtectionProvider` anahtar deposunun yalnızca bellek içinde tutulduğu ve herhangi bir yedekleme deposuna yazılmadığı temel bir uygulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="aa0d4-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="aa0d4-108">Her bir örneği `EphemeralDataProtectionProvider` kendi benzersiz ana anahtarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="aa0d4-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="aa0d4-109">Bu nedenle, `IDataProtector` bir kökü bir `EphemeralDataProtectionProvider` korumalı yük oluşturursa, bu yük yalnızca aynı örneğe sahip olan bir eşdeğer `IDataProtector` (aynı [Amaç](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) zinciri verilen) tarafından korumasız olabilir `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="aa0d4-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="aa0d4-110">Aşağıdaki örnek, bir örneğini örneklemenizi ve `EphemeralDataProtectionProvider` verileri korumak ve korumayı kaldırmak için kullanmayı göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="aa0d4-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
