---
title: ASP.NET Core için amaç dizeleri
author: rick-anderson
description: ASP.NET Core veri koruma API 'Lerinde amaç dizelerinin nasıl kullanıldığını öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 1119c45570338f629a3ab7adbd43361529aa23e7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626928"
---
# <a name="purpose-strings-in-aspnet-core"></a>ASP.NET Core için amaç dizeleri

<a name="data-protection-consumer-apis-purposes"></a>

Kullanan bileşenler `IDataProtectionProvider` , metoduna benzersiz bir *amaçlar* parametresi iletmelidir `CreateProtector` . Amaç *parametresi* , kök şifreleme anahtarları aynı olsa bile, şifreleme tüketicileri arasında yalıtım sağladığından, veri koruma sisteminin güvenliğine ait olur.

Bir tüketici amacını belirttiğinde, amaç dizesi, bu tüketiciye özgü şifreleme alt anahtarlarını türetmek için kök şifreleme anahtarlarıyla birlikte kullanılır. Bu, tüketiciyi uygulamadaki diğer tüm şifreleme tüketicilerinden yalıtır: başka hiçbir bileşen yüklerini okuyamadığı için başka bir bileşen tarafından okunamaz. Bu yalıtım Ayrıca, bileşene karşı uygun olmayan tüm saldırı kategorilerini da işler.

![Amaç diyagramı örneği](purpose-strings/_static/purposes.png)

Yukarıdaki diyagramda, `IDataProtector` A ve B örnekleri birbirini yalnızca kendi yüklerini **okuyamaz** .

Amaç dizesinin gizli olması gerekmez. Tek bir iyi davranmış bileşenin aynı amaç dizesini sağlamayabileceği anlamda benzersiz olması gerekir.

>[!TIP]
> Veri koruma API 'Lerini kullanan bileşenin ad alanını ve tür adını kullanmak, bu bilgiler hiçbir şekilde çakışmayacağı için iyi bir parmak kuralıdır.
>
>En küçük taşıyıcı belirteçlerinden sorumlu olan contoso tarafından yazılmış bir bileşen, kendi amaç dizesi olarak contoso. Security. Yataertoken kullanabilir. Ya da daha iyi bir deyişle, kendi amaç dizesi olarak contoso. Security. Yataertoken. v1 kullanabilir. Sürüm numarasını eklemek, gelecekteki bir sürümün amaç olarak contoso. Security. Yataertoken. v2 kullanmasına izin verir ve farklı sürümler, yük olduğu kadar bir diğerinden tamamen yalıtılacaktır.

İçin amaçları parametresi `CreateProtector` bir dize dizisi olduğundan, yukarıdaki gibi olarak belirtilir `[ "Contoso.Security.BearerToken", "v1" ]` . Bu, bir amaç hiyerarşisi oluşturmayı ve veri koruma sistemiyle çok kiracılı senaryolar olasılığını açar.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Bileşenler, güvenilmeyen Kullanıcı girişinin, amaç zinciri için tek giriş kaynağı olması için izin vermemelidir.
>
>Örneğin, güvenli iletileri depolamaktan sorumlu olan contoso. Messaging. SecureMessage bileşenini göz önünde bulundurun. Güvenli mesajlaşma bileşeni çağırırma `CreateProtector([ username ])` , kötü niyetli bir Kullanıcı, bileşeni çağırmak için "contoso. Security. yataertoken" Kullanıcı adına sahip bir hesap oluşturabilir `CreateProtector([ "Contoso.Security.BearerToken" ])` ve bu sayede güvenli mesajlaşma sisteminin kimlik doğrulama belirteçleri olarak algılanan krem yükleri olmasına neden olabilir.
>
>Mesajlaşma bileşeni için daha iyi bir amaç zinciri `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , doğru yalıtım sağlar.

Ve davranışları tarafından sunulan yalıtım, ve `IDataProtectionProvider` , `IDataProtector` ve amaçları aşağıdaki gibidir:

* Belirli bir `IDataProtectionProvider` nesne için, `CreateProtector` yöntemi `IDataProtector` `IDataProtectionProvider` kendisini oluşturan nesnesine ve yöntemine geçirilen amaçlar parametresine benzersiz bir şekilde bağlanmış bir nesne oluşturur.

* Amaç parametresi null olmamalıdır. (Eğer amaçları bir dizi olarak belirtilmişse bu, dizinin sıfır uzunlukta olmaması ve dizinin tüm öğelerinin null olmaması gerektiğini gösterir.) Boş bir dize amacına Teknik olarak izin verilir ancak önerilmez.

* İki amaç bağımsız değişkeni aynı sırada ve yalnızca aynı dizeleri içeriyorsa (bir sıra karşılaştırıcısı kullanarak) eşdeğerdir. Tek bir amaç bağımsız değişkeni, karşılık gelen tek öğeli amaçlar dizisine eşdeğerdir.

* İki `IDataProtector` nesne yalnızca eşdeğer bir parametre ile eşdeğer nesnelerden oluşturulduklarında eşdeğerdir `IDataProtectionProvider` .

* Belirli bir `IDataProtector` nesne için bir çağrısı, `Unprotect(protectedData)` `unprotectedData` ve yalnızca `protectedData := Protect(unprotectedData)` denk bir nesne için ise, orijinali döndürür `IDataProtector` .

> [!NOTE]
> Bazı bileşenlerin, başka bir bileşenle çakışacak bilinen bir amaç dizesi kasıtlı olarak seçtiği durumu dikkate almıyoruz. Bu tür bir bileşen temelde kötü amaçlı olarak değerlendirilir ve bu sistem kötü amaçlı kodun çalışan işlemin içinde zaten çalıştığı olayda güvenlik garantisi sağlamaya yönelik değildir.
