---
title: ASP.NET Core 'de amaç hiyerarşisi ve çok kiracılı
author: rick-anderson
description: ASP.NET Core veri koruma API 'Leri ile ilişkili olduğu için amaç dize hiyerarşisi ve çok kiracılı olma hakkında bilgi edinin.
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
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 84714cd852a3cbc7ff77d0fd0c88931536dead1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052792"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>ASP.NET Core 'de amaç hiyerarşisi ve çok kiracılı

`IDataProtector`Ayrıca, örtük bir şekilde de olduğu `IDataProtectionProvider` için, bir amaç birlikte zincirlenebilir. Bu anlamda `provider.CreateProtector([ "purpose1", "purpose2" ])` ile eşdeğerdir `provider.CreateProtector("purpose1").CreateProtector("purpose2")` .

Bu, veri koruma sistemi aracılığıyla bazı ilginç hiyerarşik ilişkilerin kullanılmasına olanak tanır. [Contoso. Messaging. securemessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)dosyasındaki önceki örnekte, securemessage bileşeni bir `provider.CreateProtector("Contoso.Messaging.SecureMessage")` kez önyükleyebilir ve sonucu özel bir alana önbelleğe alabilir `_myProvider` . Daha sonra, yapılan çağrılar aracılığıyla daha sonra oluşturulabilir `_myProvider.CreateProtector("User: username")` ve bu koruyucular tek tek iletilerin güvenliğini sağlamak için kullanılır.

Bu, ayrıca çevrilebilir. Birden çok kiracıyı barındıran tek bir mantıksal uygulamayı düşünün (bir CMS makul görünmektedir) ve her kiracı kendi kimlik doğrulama ve durum yönetim sistemiyle yapılandırılabilir. Şemsiye uygulaması tek bir ana sağlayıcıya sahiptir ve `provider.CreateProtector("Tenant 1")` `provider.CreateProtector("Tenant 2")` her kiracıya veri koruma sisteminin yalıtılmış dilimine vermek için ve çağırır. Kiracılar daha sonra kendi ihtiyaçlarına göre kendi bireysel koruyucularını türetebilir, ancak bunları ne kadar zor bir şekilde, sistemdeki diğer kiracılarla çakıştıkları bir koruyucu oluşturamazlar. Grafik olarak, aşağıda gösterildiği gibi gösterilir.

![Çoklu kiracı amaçları](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Bu, şemsiye uygulamanın hangi API 'Lerin tek tek kiracılar için kullanılabilir olduğunu ve kiracıların sunucuda rastgele kod yürütemeyeceğini varsayar. Bir kiracı rastgele kod yürütebileceği gibi, yalıtım garantilerini bölmek için özel yansıma gerçekleştirebilir veya doğrudan ana anahtar malzemesini okuyabilir ve istedikleri alt anahtarları türetebilir.

Veri koruma sistemi aslında, varsayılan kullanıma hazır yapılandırmasında çok kiracılı bir sıralama kullanır. Varsayılan olarak ana anahtar malzemesi, çalışan işlem hesabının kullanıcı profili klasöründe (veya IIS uygulama havuzu kimlikleri için kayıt defterinde) depolanır. Ancak, çok sayıda uygulama çalıştırmak için tek bir hesap kullanmak oldukça yaygın bir şeydir ve bu nedenle, tüm bu uygulamalar ana anahtar malzemesini paylaşmayı sona erdirmek için kullanılır. Bunu çözümlemek için, veri koruma sistemi otomatik olarak benzersiz bir uygulama başına tanımlayıcıyı genel amaçlı zincirde ilk öğe olarak ekler. Bu örtük amaç, her uygulamayı sistem içinde benzersiz bir kiracı olarak kabul ederek [tek tek uygulamaları diğerlerinden yalıtmak](xref:security/data-protection/configuration/overview#per-application-isolation) için kullanılır ve koruyucu oluşturma işlemi yukarıdaki görüntüyle aynı şekilde görünür.
