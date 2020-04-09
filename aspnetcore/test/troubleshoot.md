---
title: Sorun giderme ve Hata Ayıklama ASP.NET Core projeleri
author: Rick-Anderson
description: ASP.NET Core projeleri ile uyarıları ve hataları anlayın ve sorun giderin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511515"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Sorun giderme ve Hata Ayıklama ASP.NET Core projeleri

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Aşağıdaki bağlantılar sorun giderme kılavuzu sağlar:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Konferansı (Londra, 2018): ASP.NET Temel Uygulamalarda Sorunların Teşhisi](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET Blog: Temel Performans Sorunları ASP.NET Sorun Giderme](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Çekirdek SDK uyarıları

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>.NET Core SDK'nın hem 32 bit hem de 64 bit sürümleri yüklenir

ASP.NET Core için **Yeni Proje** iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:

> .NET Core SDK'nın hem 32 bit hem de 64 bit sürümleri yüklenir. Yalnızca\\'C: Program\\Files dotnet\\sdk'\\adresinde yüklü 64 bit sürümlerden şablonlar görüntülenir.

Bu uyarı, [.NET Core SDK'nın](https://dotnet.microsoft.com/download/dotnet-core) hem 32 bit (x86) hem de 64 bit (x64) sürümleri yüklendiğinde görüntülenir. Her iki sürümün de yüklenmesinin yaygın nedenleri şunlardır:

* .NET Core SDK yükleyicisini 32 bit lik bir makine kullanarak indirdiniz, ancak sonra kopyalayıp 64 bit lik bir makineye yükledin.
* 32-bit .NET Core SDK başka bir uygulama tarafından yüklendi.
* Yanlış sürüm indirildi ve yüklendi.

Bu uyarıyı önlemek için 32 bit .NET Core SDK'yı kaldırın. **Denetim Masası** > **Programlarından ve Özelliklerinden** > Kaldırma veya**Programı Değiştir.** Uyarının neden oluştuğunu ve bunun sonuçlarını anlıyorsanız, uyarıyı yok sayabilirsiniz.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK birden çok konuma yüklenir

ASP.NET Core için **Yeni Proje** iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:

> .NET Core SDK birden çok konuma yüklenir. Yalnızca\\'C: Program\\Files dotnet\\sdk'\\yüklü SDK'lardan şablonlar görüntülenir.

*C:\\Program Dosyaları\\dotnet\\sdk\\*dışında bir dizinde .NET Core SDK en az bir yükleme varsa bu mesajı görürsünüz. Genellikle bu, .NET Core SDK MSI yükleyicisi yerine kopyala/yapıştır kullanarak bir makineye dağıtıldığında gerçekleşir.

Bu uyarıyı önlemek için tüm 32 bit .NET Core SDK'ları ve çalışma sürelerini kaldırın. **Denetim Masası** > **Programlarından ve Özelliklerinden** > Kaldırma veya**Programı Değiştir.** Uyarının neden oluştuğunu ve bunun sonuçlarını anlıyorsanız, uyarıyı yok sayabilirsiniz.

### <a name="no-net-core-sdks-were-detected"></a>.NET Çekirdek SDK'sı algılanmadı

* Visual Studio **Yeni Proje** ASP.NET Core iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:

  > .NET Çekirdek SDK'ları algılanmadı, ortam değişkenine `PATH`dahil edildiklerinden emin olun.

* Bir `dotnet` komut uyrurken, uyarı şu şekilde görünür:

  > Yüklü dotnet SDK'ları bulmak mümkün değildi.

Bu uyarılar, ortam `PATH` değişkeni makinedeki herhangi bir .NET Core SDK'ya işaret etmediğinde görünür. Bu sorunu çözmek için:

* .NET Core SDK'yı yükleyin. [.NET İndirmelerden](https://dotnet.microsoft.com/download)en son yükleyiciyi edinin.
* Ortam değişkeninin `PATH` SDK'nın yüklü olduğu konuma`C:\Program Files\dotnet\` (64-bit/x64 `C:\Program Files (x86)\dotnet\` veya 32-bit/x86) işaret ettiğini doğrulayın. SDK yükleyicisi normalde `PATH`. Aynı makineye her zaman aynı bitlikteki SDK'ları ve çalışma sürelerini yükleyin.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core Hosting Paketini yükledikten sonra Eksik SDK

[.NET Çekirdek Barındırma Paketi'nin](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) `PATH` yüklenmesi, .NET Core 'un 32 bit (x86) sürümünü işaret etmek`C:\Program Files (x86)\dotnet\`için .NET Core çalışma zamanını değiştirir. Bu, 32 bit (x86) .NET Core `dotnet` komutu kullanıldığında SDK'ların eksik olması yla sonuçlanabilir ([.NET Çekirdek SDK'ları algılanmaz).](#no-net-core-sdks-were-detected) Bu sorunu gidermek `C:\Program Files\dotnet\` için, `C:\Program Files (x86)\dotnet\` ''de `PATH`önce bir konuma geçin.

## <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alma

Bir uygulama isteklere yanıt verebiliyorsa, ara yazılımkullanarak uygulamadan aşağıdaki verileri elde edebilirsiniz:

* İstek &ndash; Yöntemi, şema, ana bilgisayar, yol tabanı, yol, sorgu dizesi, üstbilgi
* Bağlantı &ndash; Uzak IP adresi, uzak bağlantı noktası, yerel IP adresi, yerel bağlantı noktası, istemci sertifikası
* Kimlik &ndash; Adı, görüntü adı
* Yapılandırma ayarları
* Ortam değişkenleri

Yöntemin [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) istek işleme ardışık hattının `Startup.Configure` başına aşağıdaki ara yazılım kodunu yerleştirin. Ortamın, kodun yalnızca Geliştirme ortamında yürütülmediğinden emin olmak için ara yazılım çalıştırılmadan önce denetlenir.

Ortamı elde etmek için aşağıdaki yaklaşımlardan birini kullanın:

* `IHostingEnvironment` Yöntemiçine `Startup.Configure` enjekte ve yerel değişken ile çevre kontrol edin. Aşağıdaki örnek kod bu yaklaşımı göstermektedir.

* Ortamı sınıftaki `Startup` bir özelliğe atayın. Özelliği kullanarak ortamı denetleyin (örneğin, `if (Environment.IsDevelopment())`).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>Temel uygulamaları ASP.NET hata ayıklama

Aşağıdaki bağlantılar, ASP.NET Core uygulamaları hakkında bilgi sağlar.

* [Linux'ta ASP Core Hata Ayıklama](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [SSH üzerinden Unix üzerinde .NET Core hata ayıklama](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Quickstart: Visual Studio hata ayıklama ile ASP.NET hata ayıklama](/visualstudio/debugger/quickstart-debug-aspnet)
* Daha fazla hata ayıklama bilgisi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/2960) bakın.
