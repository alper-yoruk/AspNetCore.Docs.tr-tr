---
title: SignalRAPI tasarımı konuları
author: anurse
description: SignalRUygulamanızın sürümleri arasında uyumluluk için API 'leri tasarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/api-design
ms.openlocfilehash: 9ad8d30da552d3d3084534b8c7ca57386ad111ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407804"
---
# <a name="signalr-api-design-considerations"></a>SignalRAPI tasarımı konuları

, [Andrew Stanton-nurte](https://twitter.com/anurse)

Bu makalede, yapı SignalR tabanlı API 'ler için rehberlik sunulmaktadır.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Geriye dönük uyumluluk sağlamak için özel nesne parametreleri kullanın

Bir SignalR hub yöntemine (istemcide veya sunucuda) parametre eklenmesi, bir *son değişiklikten*sonra yapılır. Bu, eski istemcilerin/sunucuların, yöntemi uygun sayıda parametre olmadan çağırmaya çalıştıklarında hata alabileceği anlamına gelir. Ancak, özel bir nesne parametresine özellikler eklemek, bir son değişiklik **değildir** . Bu, istemci veya sunucu üzerindeki değişikliklere dayanıklı olan uyumlu API 'Ler tasarlamak için kullanılabilir.

Örneğin, aşağıdaki gibi bir sunucu tarafı API göz önünde bulundurun:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript istemcisi şu şekilde kullanarak bu yöntemi çağırır `invoke` :

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Daha sonra sunucu yöntemine ikinci bir parametre eklerseniz, eski istemciler bu parametre değerini sağlamaz. Örneğin:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Eski istemci bu yöntemi çağırmayı denediğinde şuna benzer bir hata olur:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Sunucusunda şöyle bir günlük iletisi görürsünüz:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Eski istemci yalnızca bir parametre gönderdi, ancak yeni sunucu API 'SI iki parametre gerektirdi. Özel nesneleri parametre olarak kullanmak daha fazla esneklik sağlar. Özgün API 'yi özel bir nesne kullanacak şekilde yeniden tasarlayalım:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Artık istemci, yöntemi çağırmak için bir nesne kullanır:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Bir parametre eklemek yerine, nesnesine bir özellik ekleyin `TotalLengthRequest` :

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Eski istemci tek bir parametre gönderdiğinde, ek `Param2` özellik bırakılır `null` . `Param2` `null` ' İ denetleyerek ve varsayılan bir değer uygulayarak, eski bir istemci tarafından gönderilen bir iletiyi tespit edebilirsiniz. Yeni bir istemci her iki parametreyi de gönderebilir.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

Aynı teknik, istemcide tanımlanan yöntemler için de kullanılır. Sunucu tarafında özel bir nesne gönderebilirsiniz:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

İstemci tarafında, `Message` bir parametre kullanmak yerine özelliğe erişirsiniz:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Daha sonra iletinin gönderisini yüke eklemeye karar verirseniz, nesnesine bir özellik ekleyin:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Eski istemciler `Sender` değeri beklemdiklerinden, yok sayacaktır. Yeni bir istemci, yeni özelliği okumak üzere güncelleyerek kabul edebilir:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

Bu durumda, yeni istemci Ayrıca değer sağlamayan eski bir sunucu için de toleranslı olur `Sender` . Eski sunucu `Sender` değeri sağlamadığından, istemci bu değere erişmeden önce varolup olmadığını kontrol eder.
