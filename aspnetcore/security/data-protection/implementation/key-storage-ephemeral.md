---
title: ASP.NET Core kısa ömürlü veri koruma sağlayıcıları
author: rick-anderson
description: ASP.NET Core kısa ömürlü veri koruma sağlayıcılarının uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052708"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>ASP.NET Core kısa ömürlü veri koruma sağlayıcıları

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Bir uygulamanın bir throwaway ihtiyacı olan senaryolar vardır `IDataProtectionProvider` . Örneğin, geliştirici yalnızca tek bir konsol uygulamasında çalışabilir ya da uygulamanın kendisi geçici (betikleştirilmiş veya bir birim test projesi). Bu senaryoları desteklemek için, [Microsoft. AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) paketi bir tür içerir `EphemeralDataProtectionProvider` . Bu tür, `IDataProtectionProvider` anahtar deposunun yalnızca bellek içinde tutulduğu ve herhangi bir yedekleme deposuna yazılmadığı temel bir uygulama sağlar.

Her bir örneği `EphemeralDataProtectionProvider` kendi benzersiz ana anahtarını kullanır. Bu nedenle, `IDataProtector` bir kökü bir `EphemeralDataProtectionProvider` korumalı yük oluşturursa, bu yük yalnızca aynı örneğe sahip olan bir eşdeğer `IDataProtector` (aynı [Amaç](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) zinciri verilen) tarafından korumasız olabilir `EphemeralDataProtectionProvider` .

Aşağıdaki örnek, bir örneğini örneklemenizi ve `EphemeralDataProtectionProvider` verileri korumak ve korumayı kaldırmak için kullanmayı göstermektedir.

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
