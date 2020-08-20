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
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="02546-103">ASP.NET Core için amaç dizeleri</span><span class="sxs-lookup"><span data-stu-id="02546-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="02546-104">Kullanan bileşenler `IDataProtectionProvider` , metoduna benzersiz bir *amaçlar* parametresi iletmelidir `CreateProtector` .</span><span class="sxs-lookup"><span data-stu-id="02546-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="02546-105">Amaç *parametresi* , kök şifreleme anahtarları aynı olsa bile, şifreleme tüketicileri arasında yalıtım sağladığından, veri koruma sisteminin güvenliğine ait olur.</span><span class="sxs-lookup"><span data-stu-id="02546-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="02546-106">Bir tüketici amacını belirttiğinde, amaç dizesi, bu tüketiciye özgü şifreleme alt anahtarlarını türetmek için kök şifreleme anahtarlarıyla birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="02546-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="02546-107">Bu, tüketiciyi uygulamadaki diğer tüm şifreleme tüketicilerinden yalıtır: başka hiçbir bileşen yüklerini okuyamadığı için başka bir bileşen tarafından okunamaz.</span><span class="sxs-lookup"><span data-stu-id="02546-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="02546-108">Bu yalıtım Ayrıca, bileşene karşı uygun olmayan tüm saldırı kategorilerini da işler.</span><span class="sxs-lookup"><span data-stu-id="02546-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Amaç diyagramı örneği](purpose-strings/_static/purposes.png)

<span data-ttu-id="02546-110">Yukarıdaki diyagramda, `IDataProtector` A ve B örnekleri birbirini yalnızca kendi yüklerini **okuyamaz** .</span><span class="sxs-lookup"><span data-stu-id="02546-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="02546-111">Amaç dizesinin gizli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="02546-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="02546-112">Tek bir iyi davranmış bileşenin aynı amaç dizesini sağlamayabileceği anlamda benzersiz olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="02546-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="02546-113">Veri koruma API 'Lerini kullanan bileşenin ad alanını ve tür adını kullanmak, bu bilgiler hiçbir şekilde çakışmayacağı için iyi bir parmak kuralıdır.</span><span class="sxs-lookup"><span data-stu-id="02546-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="02546-114">En küçük taşıyıcı belirteçlerinden sorumlu olan contoso tarafından yazılmış bir bileşen, kendi amaç dizesi olarak contoso. Security. Yataertoken kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="02546-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="02546-115">Ya da daha iyi bir deyişle, kendi amaç dizesi olarak contoso. Security. Yataertoken. v1 kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="02546-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="02546-116">Sürüm numarasını eklemek, gelecekteki bir sürümün amaç olarak contoso. Security. Yataertoken. v2 kullanmasına izin verir ve farklı sürümler, yük olduğu kadar bir diğerinden tamamen yalıtılacaktır.</span><span class="sxs-lookup"><span data-stu-id="02546-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="02546-117">İçin amaçları parametresi `CreateProtector` bir dize dizisi olduğundan, yukarıdaki gibi olarak belirtilir `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="02546-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="02546-118">Bu, bir amaç hiyerarşisi oluşturmayı ve veri koruma sistemiyle çok kiracılı senaryolar olasılığını açar.</span><span class="sxs-lookup"><span data-stu-id="02546-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="02546-119">Bileşenler, güvenilmeyen Kullanıcı girişinin, amaç zinciri için tek giriş kaynağı olması için izin vermemelidir.</span><span class="sxs-lookup"><span data-stu-id="02546-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="02546-120">Örneğin, güvenli iletileri depolamaktan sorumlu olan contoso. Messaging. SecureMessage bileşenini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="02546-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="02546-121">Güvenli mesajlaşma bileşeni çağırırma `CreateProtector([ username ])` , kötü niyetli bir Kullanıcı, bileşeni çağırmak için "contoso. Security. yataertoken" Kullanıcı adına sahip bir hesap oluşturabilir `CreateProtector([ "Contoso.Security.BearerToken" ])` ve bu sayede güvenli mesajlaşma sisteminin kimlik doğrulama belirteçleri olarak algılanan krem yükleri olmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="02546-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="02546-122">Mesajlaşma bileşeni için daha iyi bir amaç zinciri `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , doğru yalıtım sağlar.</span><span class="sxs-lookup"><span data-stu-id="02546-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="02546-123">Ve davranışları tarafından sunulan yalıtım, ve `IDataProtectionProvider` , `IDataProtector` ve amaçları aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="02546-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="02546-124">Belirli bir `IDataProtectionProvider` nesne için, `CreateProtector` yöntemi `IDataProtector` `IDataProtectionProvider` kendisini oluşturan nesnesine ve yöntemine geçirilen amaçlar parametresine benzersiz bir şekilde bağlanmış bir nesne oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02546-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="02546-125">Amaç parametresi null olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="02546-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="02546-126">(Eğer amaçları bir dizi olarak belirtilmişse bu, dizinin sıfır uzunlukta olmaması ve dizinin tüm öğelerinin null olmaması gerektiğini gösterir.) Boş bir dize amacına Teknik olarak izin verilir ancak önerilmez.</span><span class="sxs-lookup"><span data-stu-id="02546-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="02546-127">İki amaç bağımsız değişkeni aynı sırada ve yalnızca aynı dizeleri içeriyorsa (bir sıra karşılaştırıcısı kullanarak) eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="02546-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="02546-128">Tek bir amaç bağımsız değişkeni, karşılık gelen tek öğeli amaçlar dizisine eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="02546-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="02546-129">İki `IDataProtector` nesne yalnızca eşdeğer bir parametre ile eşdeğer nesnelerden oluşturulduklarında eşdeğerdir `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="02546-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="02546-130">Belirli bir `IDataProtector` nesne için bir çağrısı, `Unprotect(protectedData)` `unprotectedData` ve yalnızca `protectedData := Protect(unprotectedData)` denk bir nesne için ise, orijinali döndürür `IDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="02546-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="02546-131">Bazı bileşenlerin, başka bir bileşenle çakışacak bilinen bir amaç dizesi kasıtlı olarak seçtiği durumu dikkate almıyoruz.</span><span class="sxs-lookup"><span data-stu-id="02546-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="02546-132">Bu tür bir bileşen temelde kötü amaçlı olarak değerlendirilir ve bu sistem kötü amaçlı kodun çalışan işlemin içinde zaten çalıştığı olayda güvenlik garantisi sağlamaya yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="02546-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
