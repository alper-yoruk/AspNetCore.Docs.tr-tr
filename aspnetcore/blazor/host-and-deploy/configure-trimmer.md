---
title: ASP.NET Core için ayarlayıcısı yapılandırma Blazor
author: guardrex
description: Uygulama oluştururken ara dil (IL) bağlayıcı (kırpıcıyı) denetimini nasıl denetleyeceğinizi öğrenin Blazor .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055808"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>ASP.NET Core için ayarlayıcısı yapılandırma Blazor

By [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly yayımlanan çıktının boyutunu azaltmak için [ara dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) kırpmasını gerçekleştirir.

Uygulamanın kırpılırken boyut için en iyi duruma getirmek, ancak bu etkilere sebep olabilir. Yansıma ve ilgili dinamik özellikleri kullanan uygulamalar kırpılmadığınızda kesilebilir, çünkü ayarlayıcısı dinamik davranışı öğrenmez ve çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemeyebilir. Bu tür uygulamaları kırpmak için, kırpıcıyı kodda yansıma tarafından gereken tüm türler ve uygulamanın bağımlı olduğu paketler veya çerçeveler hakkında bilgilendirilmesi gerekir.

Kırpılan uygulamanın dağıtıldıktan sonra düzgün çalıştığından emin olmak için, geliştirme sırasında yayımlanan çıktıyı sık sık test etmek önemlidir.

.NET uygulamalarının bölünmesi, `PublishTrimmed` MSBuild özelliği uygulamanın proje dosyasında olarak ayarlanarak devre dışı bırakılabilir `false` :

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
Ayarlayıcısı yapılandırmak için ek seçenekler, [kırpma seçeneklerinde](/dotnet/core/deploying/trimming-options)bulunabilir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kendi içinde bulunan dağıtımları ve yürütülebilir dosyaları kırp](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
