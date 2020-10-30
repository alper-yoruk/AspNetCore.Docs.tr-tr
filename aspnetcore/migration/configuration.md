---
title: Yapılandırmayı ASP.NET Core geçir
author: ardalis
description: ASP.NET MVC projesinden yapılandırmayı ASP.NET Core MVC projesine geçirmeyi öğrenin.
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
uid: migration/configuration
ms.openlocfilehash: d84204c8c791bfaf36432462cde3a42c294c7966
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059799"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Yapılandırmayı ASP.NET Core geçir

, [Steve Smith](https://ardalis.com/) ve [Scott Ade](https://scottaddie.com) tarafından

Önceki makalede, [bir ASP.NET MVC projesini ASP.NET Core MVC 'ye geçirmeye](xref:migration/mvc)başladık. Bu makalede, yapılandırmayı geçiririz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Kurulum yapılandırması

ASP.NET Core artık, önceki ASP.NET sürümleri tarafından kullanılan *Global. asax* ve *web.config* dosyalarını kullanmaz. Önceki ASP.NET sürümlerinde, uygulama başlangıç mantığı `Application_StartUp` *Global. asax* içindeki bir yönteme yerleştirildi. Daha sonra, ASP.NET MVC 'de projenin köküne bir *Startup.cs* dosyası eklenmiştir; ve uygulama başlatıldığında çağırılır. ASP.NET Core, tüm başlangıç mantığını *Startup.cs* dosyasına yerleştirerek bu yaklaşımı tamamen benimsemiştir.

*web.config* dosya Ayrıca ASP.NET Core değiştirilmiştir. Yapılandırma, *Startup.cs* bölümünde açıklanan uygulama başlatma yordamının bir parçası olarak artık yapılandırılabilir. Yapılandırma XML dosyalarını kullanmaya devam edebilir, ancak genellikle ASP.NET Core projeler yapılandırma değerlerini gibi JSON biçimli bir dosyaya yerleştirir *appsettings.json* . ASP.NET Core yapılandırma sistemi, ortama özgü değerler için [daha güvenli ve sağlam bir konum](xref:security/app-secrets) sağlayabilen ortam değişkenlerine de kolayca erişebilir. Bu, kaynak denetimine denetlenmemelidir bağlantı dizeleri ve API anahtarları gibi gizli diziler için özellikle doğrudur. ASP.NET Core yapılandırma hakkında daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .

Bu makalede, [önceki makaleden](xref:migration/mvc)kısmen geçirilmiş ASP.NET Core projesi ile başlıyoruz. Yapılandırmayı ayarlamak için, aşağıdaki oluşturucuyu ve özelliğini projenin kökünde bulunan *Startup.cs* dosyasına ekleyin:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Bu noktada, *Startup.cs* dosyasının derlenmeyeceğini, ancak yine de aşağıdaki ifadeyi eklememiz gerektiğini unutmayın `using` :

```csharp
using Microsoft.Extensions.Configuration;
```

*appsettings.json* Uygun öğe şablonunu kullanarak projenin köküne bir dosya ekleyin:

![AppSettings JSON Ekle](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Yapılandırma ayarlarını web.config geçir

ASP.NET MVC projemiz, gereken *web.config* veritabanı bağlantı dizesini `<connectionStrings>` öğesine içeriyordu. ASP.NET Core projemizdeki bu bilgileri dosyada depolayacağız *appsettings.json* . *appsettings.json* ' İ açın ve zaten şunları içerdiğini unutmayın:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Yukarıda gösterilen vurgulanan satırda veritabanının adını **_CHANGE_ME** ' dan veritabanınızın adına değiştirin.

## <a name="summary"></a>Özet

ASP.NET Core, uygulamanın tüm başlangıç mantığını, gerekli hizmetlerin ve bağımlılıkların tanımlanmasının ve yapılandırılabileceği tek bir dosyaya koyar. *web.config* dosyasını, JSON gibi çeşitli dosya biçimlerinden ve ortam değişkenlerinin yanı sıra çeşitli dosya biçimlerinden faydalanabilir esnek bir yapılandırma özelliği ile değiştirir.
