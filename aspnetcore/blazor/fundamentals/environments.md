---
title: ASP.NET Core Blazor ortamları
author: guardrex
description: "' Deki ortamlar Blazor , bir uygulamanın ortamını ayarlama dahil hakkında bilgi edinin Blazor WebAssembly ."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 3d9b0cab42a826c7a5868324d891e597cd9ed986
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97678294"
---
# <a name="aspnet-core-no-locblazor-environments"></a>ASP.NET Core Blazor ortamları

> [!NOTE]
> Bu konu için geçerlidir Blazor WebAssembly . Uygulamalar için kullanılan yaklaşımları açıklayan ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için Blazor Server bkz <xref:fundamentals/environments> ..

Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır. Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.

BlazorBarındırılan bir çözümün istemci tarafı uygulaması ( *`Client`* ), Blazor WebAssembly *`Server`* tarayıcıyı tarayıcıya ileten bir ara yazılım aracılığıyla çözümün uygulamasından bir ortam belirler. *`Server`* Uygulama, `blazor-environment` üst bilgi değeri olarak ortama sahip adlı bir üst bilgi ekler. *`Client`* Uygulama üstbilgiyi okur. *`Server`* Çözüm uygulaması ASP.NET Core bir uygulamadır, bu nedenle ortamın nasıl yapılandırılacağı hakkında daha fazla bilgi içinde bulunur <xref:fundamentals/environments> .

Blazor WebAssemblyYerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler. Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.

IIS için aşağıdaki örnekte, özel üst bilgi ( `blazor-environment` ) yayımlanan `web.config` dosyaya eklenir. `web.config`Dosya `bin/Release/{TARGET FRAMEWORK}/publish` klasöründe bulunur, burada yer tutucu `{TARGET FRAMEWORK}` hedef çerçevedir:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> `web.config`Uygulama klasöre yayımlandığında üzerine yazılmayan IIS için özel bir dosya kullanmak istiyorsanız `publish` , bkz <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> ..

Ekleme tarafından bir bileşende uygulamanın ortamını edinin <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> ve <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> özelliği okuyun.

`Pages/ReadEnvironment.razor`:

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

Başlangıç sırasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> ana bilgisayar Oluşturucu kodunda ortama özgü mantık sağlayan özelliği aracılığıyla özelliğini kullanıma sunar.

İçinde `Program.Main` `Program.cs` :

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions>Geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin ver aracılığıyla aşağıdaki kullanışlı uzantı yöntemleri verilmiştir:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

İçinde `Program.Main` `Program.cs` :

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir <xref:Microsoft.AspNetCore.Components.NavigationManager> .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/environments>
