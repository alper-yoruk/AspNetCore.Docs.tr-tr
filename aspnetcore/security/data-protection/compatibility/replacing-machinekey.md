---
title: ASP.NET Core ASP.NET machineKey değiştirme
author: rick-anderson
description: Yeni ve daha güvenli bir veri koruma sisteminin kullanımına izin vermek için ASP.NET içindeki machineKey nasıl değiştirileceğini öğrenin.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 8cae0b8f1c4582e272061ff87868b32568dfe595
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625576"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>ASP.NET Core ASP.NET machineKey değiştirme

<a name="compatibility-replacing-machinekey"></a>

`<machineKey>`ASP.NET içindeki öğesinin uygulanması [değiştirilebilir](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Bu, ASP.NET şifreleme yordamlarına yapılan çağrıların, yeni veri koruma sistemi dahil olmak üzere bir değiştirme veri koruma mekanizması aracılığıyla yönlendirilmesini sağlar.

## <a name="package-installation"></a>Paket yüklemesi

> [!NOTE]
> Yeni veri koruma sistemi yalnızca .NET 4.5.1 veya üstünü hedefleyen mevcut bir ASP.NET uygulamasına yüklenebilir. Uygulama .NET 4,5 veya daha düşük bir düzeye hedefliyorsa yükleme başarısız olur.

Yeni veri koruma sistemini var olan bir ASP.NET 4.5.1 + projesine yüklemek için, Microsoft.AspNetCore.DataProtection.SystemWeb paketini yüklemelisiniz. Bu, [varsayılan yapılandırma](xref:security/data-protection/configuration/default-settings) ayarları kullanılarak veri koruma sisteminin örneğini oluşturur.

Paketi yüklediğinizde, ASP.NET 'e, form kimlik doğrulaması, Görünüm durumu ve MachineKey. Protect çağrıları dahil olmak üzere [çoğu şifreleme işlemi](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)için bunu kullanmasını söyleyen *Web.config* bir çizgi ekler. Eklenen satır aşağıdaki gibi okur.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Yeni veri koruma sisteminin, `__VIEWSTATE` Aşağıdaki örnekte olduğu gibi "CfDJ8" ile başlaması gereken alanları inceleyerek, etkin olup olmadığını söyleyebilirsiniz. "CfDJ8", veri koruma sistemi tarafından korunan bir yükü tanımlayan Magic "09 F0 C9 F0" üstbilgisinin Base64 gösterimidir.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Paket yapılandırması

Veri koruma sistemi, varsayılan sıfır kurulum yapılandırmasıyla birlikte oluşturulur. Ancak, varsayılan anahtarlar yerel dosya sistemine kalıcı olduğundan, bu, bir gruba dağıtılan uygulamalar için çalışmaz. Bu sorunu çözmek için, alt sınıfları DataProtectionStartup ve ConfigureServices metodunu geçersiz kılan bir tür oluşturarak yapılandırmayı sağlayabilirsiniz.

Aşağıda, her ikisi de anahtarların kalıcı olduğu ve REST 'de nasıl şifrelendikleri üzerinde yapılandırılmış bir özel veri koruma başlangıç türü örneği verilmiştir. Ayrıca, kendi uygulama adını sağlayarak varsayılan uygulama yalıtımı ilkesini geçersiz kılar.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> Ayrıca, `<machineKey applicationName="my-app" ... />` SetApplicationName 'e açık bir çağrı yerine kullanabilirsiniz. Bu, geliştiricilerin yapılandırmak istiyorlarsa, uygulama adı ayarlarsa, geliştiricinin bir DataProtectionStartup ile türetilmiş tür oluşturmasını zormaktan kaçınmak için kullanışlı bir mekanizmadır.

Bu özel yapılandırmayı etkinleştirmek için, Web.config dönün ve `<appSettings>` paket yüklemesinin yapılandırma dosyasına eklendiği öğeyi bulun. Aşağıdaki biçimlendirme gibi görünür:

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

Yeni oluşturduğunuz DataProtectionStartup türetilmiş türünün derleme nitelikli adı ile boş değeri girin. Uygulamanın adı DataProtectionDemo ise, bu, aşağıdaki gibi görünür.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Yeni yapılandırılan veri koruma sistemi artık uygulamanın içinde kullanıma hazırdır.
