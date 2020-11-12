---
title: Httprepın telemetrisi
author: scottaddie
description: HttpRepl tarafından toplanan telemetri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550614"
---
# <a name="httprepl-telemetry"></a>Httprepın telemetrisi

[Httprepl](xref:web-api/http-repl) , kullanım verilerini toplayan bir telemetri özelliği içerir. HttpRepl ekibinin, aracın nasıl kullanıldığını anlayabilmesi önemlidir.

## <a name="how-to-opt-out"></a>Devre dışı bırakma

Bu varsayılan olarak HttpRepl telemetri özelliği etkinleştirilmiştir. Telemetri özelliğini devre dışı bırakmak için, `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` ortam değişkenini veya olarak ayarlayın `1` `true` .

## <a name="disclosure"></a>Savunmasız

HttpRepl, aracı ilk kez çalıştırdığınızda aşağıdakine benzer bir metin görüntüler. Metin, çalıştırdığınız aracın sürümüne bağlı olarak biraz farklılık gösterebilir. Bu "ilk çalıştırma" deneyimi, Microsoft 'un veri toplamayı nasıl bildisidir.

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

"İlk çalıştırma" deneyimi metnini bastırmak için, `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` ortam değişkenini veya olarak ayarlayın `1` `true` .

## <a name="data-points"></a>Veri noktaları

Telemetri özelliği şunları yapmaz:

* Kullanıcı adları, e-posta adresleri veya URL 'Ler gibi kişisel verileri toplayın.
* HTTP isteklerinizi veya yanıtlarınızı tarayın.

Veriler, Microsoft sunucularına güvenli bir şekilde gönderilir ve kısıtlı erişim altında tutulur.

Gizliliğinizi korumak bizim için önemlidir. Telemetri özelliğinin hassas verileri toplamasını veya verilerin güvenli veya uygun şekilde işlenmekte olduğunu düşünüyorsanız, aşağıdaki eylemlerden birini gerçekleştirin:

* [DotNet/httprepl](https://github.com/dotnet/httprepl/issues) deposunda bir sorun verin.
* Araştırma için adresine bir e-posta gönderin [dotnet@microsoft.com](mailto:dotnet@microsoft.com) .

Telemetri özelliği aşağıdaki verileri toplar.

| .NET SDK sürümleri | Veriler |
|--------------|------|
| >= 5,0        | Çağırma zaman damgası. |
| >= 5,0        | Coğrafi konumu belirlemede kullanılan üç sekizli IP adresi. |
| >= 5,0        | İşletim sistemi ve sürümü. |
| >= 5,0        | Aracının üzerinde çalıştığı çalışma zamanı KIMLIĞI (RID). |
| >= 5,0        | Aracın bir kapsayıcıda çalışıp çalışmadığını belirtir. |
| >= 5,0        | Karma medya Access Control (MAC) adresi: bir makine için bir şifreleme (SHA256) karma ve benzersiz KIMLIĞI. |
| >= 5,0        | Çekirdek sürümü. |
| >= 5,0        | HttpRepl sürümü. |
| >= 5,0        | Aracın `help` , `run` veya bağımsız değişkenlerle başlatılmış olup olmadığı `connect` . Gerçek bağımsız değişken değerleri toplanmadı. |
| >= 5,0        | Komut çağrıldı (örneğin, `get` ) ve başarılı olup olmadığı. |
| >= 5,0        | Komut için,, `connect` `root` `base` veya `openapi` bağımsız değişkenlerin sağlanmış olup olmadığı. Gerçek bağımsız değişken değerleri toplanmadı. |
| >= 5,0        | Komut için `pref` , bir `get` veya `set` verilen ve hangi tercihe erişildiğine bakılmaksızın. İyi bilinen bir tercih yoksa, ad karma hale getirilir. Değer toplanmadı. |
| >= 5,0        | Komut için `set header` , ayarlanan üst bilgi adı. İyi bilinen bir üst bilgi değilse, ad karma hale getirilir. Değer toplanmadı. |
| >= 5,0        | Komutu için `connect` , için özel bir durumun `dotnet new webapi` ve tercih aracılığıyla atlanıp atlanmayacağı. |
| >= 5,0        | Tüm HTTP komutları için (örneğin, GET, POST, PUT), her bir seçeneğin belirtilmediğini belirtir. Seçeneklerin değerleri toplanmaz. |

## <a name="additional-resources"></a>Ek kaynaklar

* [.NET Core SDK telemetri](/dotnet/core/tools/telemetry)
* [Telemetri verileri .NET Core CLI](https://dotnet.microsoft.com/platform/telemetry)
