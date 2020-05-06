---
title: ASP.NET Core 'de amaç hiyerarşisi ve çok kiracılı
author: rick-anderson
description: ASP.NET Core veri koruma API 'Leri ile ilişkili olduğu için amaç dize hiyerarşisi ve çok kiracılı olma hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 73edb8082d2df263bc1e6d73fee1360fa6840514
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776779"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>ASP.NET Core 'de amaç hiyerarşisi ve çok kiracılı

Ayrıca, `IDataProtector` örtük bir `IDataProtectionProvider`şekilde de olduğu için, bir amaç birlikte zincirlenebilir. Bu anlamda `provider.CreateProtector([ "purpose1", "purpose2" ])` ile `provider.CreateProtector("purpose1").CreateProtector("purpose2")`eşdeğerdir.

Bu, veri koruma sistemi aracılığıyla bazı ilginç hiyerarşik ilişkilerin kullanılmasına olanak tanır. [Contoso. Messaging. securemessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)dosyasındaki önceki örnekte, securemessage bileşeni bir `provider.CreateProtector("Contoso.Messaging.SecureMessage")` kez önyükleyebilir ve sonucu özel `_myProvider` bir alana önbelleğe alabilir. Daha sonra, yapılan `_myProvider.CreateProtector("User: username")`çağrılar aracılığıyla daha sonra oluşturulabilir ve bu koruyucular tek tek iletilerin güvenliğini sağlamak için kullanılır.

Bu, ayrıca çevrilebilir. Birden çok kiracıyı barındıran tek bir mantıksal uygulamayı düşünün (bir CMS makul görünmektedir) ve her kiracı kendi kimlik doğrulama ve durum yönetim sistemiyle yapılandırılabilir. Şemsiye uygulaması tek bir ana sağlayıcıya sahiptir ve her kiracıya veri `provider.CreateProtector("Tenant 1")` koruma `provider.CreateProtector("Tenant 2")` sisteminin yalıtılmış dilimine vermek için ve çağırır. Kiracılar daha sonra kendi ihtiyaçlarına göre kendi bireysel koruyucularını türetebilir, ancak bunları ne kadar zor bir şekilde, sistemdeki diğer kiracılarla çakıştıkları bir koruyucu oluşturamazlar. Grafik olarak, aşağıda gösterildiği gibi gösterilir.

![Çoklu kiracı amaçları](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Bu, şemsiye uygulamanın hangi API 'Lerin tek tek kiracılar için kullanılabilir olduğunu ve kiracıların sunucuda rastgele kod yürütemeyeceğini varsayar. Bir kiracı rastgele kod yürütebileceği gibi, yalıtım garantilerini bölmek için özel yansıma gerçekleştirebilir veya doğrudan ana anahtar malzemesini okuyabilir ve istedikleri alt anahtarları türetebilir.

Veri koruma sistemi aslında, varsayılan kullanıma hazır yapılandırmasında çok kiracılı bir sıralama kullanır. Varsayılan olarak ana anahtar malzemesi, çalışan işlem hesabının kullanıcı profili klasöründe (veya IIS uygulama havuzu kimlikleri için kayıt defterinde) depolanır. Ancak, çok sayıda uygulama çalıştırmak için tek bir hesap kullanmak oldukça yaygın bir şeydir ve bu nedenle, tüm bu uygulamalar ana anahtar malzemesini paylaşmayı sona erdirmek için kullanılır. Bunu çözümlemek için, veri koruma sistemi otomatik olarak benzersiz bir uygulama başına tanımlayıcıyı genel amaçlı zincirde ilk öğe olarak ekler. Bu örtük amaç, her uygulamayı sistem içinde benzersiz bir kiracı olarak kabul ederek [tek tek uygulamaları diğerlerinden yalıtmak](xref:security/data-protection/configuration/overview#per-application-isolation) için kullanılır ve koruyucu oluşturma işlemi yukarıdaki görüntüyle aynı şekilde görünür.
