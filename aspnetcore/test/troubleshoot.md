---
title: ASP.NET Core projelerinde sorun giderme ve hata ayıklama
author: Rick-Anderson
description: ASP.NET Core projelerle uyarıları ve hataları anlayın ve sorun giderin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot
ms.openlocfilehash: bd5e74569717db26496760eaaa2760122d4b49e7
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106500"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core projelerinde sorun giderme ve hata ayıklama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Aşağıdaki bağlantılar sorun giderme kılavuzunu sağlar:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Konferansı (Londra, 2018): ASP.NET Core uygulamalarda sorunları tanılama](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET blogu: sorun giderme ASP.NET Core performans sorunları](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK uyarılar

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>.NET Core SDK hem 32-bit hem de 64-bit sürümleri yüklenir

**Yeni proje** iletişim kutusunda ASP.NET Core için aşağıdaki uyarıyı görebilirsiniz:

> .NET Core SDK hem 32-bit hem de 64-bit sürümleri yüklenir. Yalnızca ' C: \\ Program Files DotNet SDK ' konumunda yüklü olan 64 bitlik sürümlerden alınan şablonlar \\ \\ \\ görüntülenir.

Bu uyarı, [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) hem 32-bit (x86) hem de 64-bit (x64) sürümleri yüklendiğinde görüntülenir. Her iki sürümün de sık yüklenebileceği yaygın nedenler şunlardır:

* İlk olarak 32 bitlik bir makine kullanarak .NET Core SDK yükleyicisini indirdiniz, ancak bu dosyayı bir 64 bit makineye kopyaladınız ve bu makinede yüklediniz.
* 32 bit .NET Core SDK başka bir uygulama tarafından yüklendi.
* Yanlış sürüm indirildi ve yüklendi.

Bu uyarıyı engellemek için 32 bit .NET Core SDK kaldırın. **Denetim Masası**  >  **Programlar ve Özellikler**'den Kaldır  >  **bir programı kaldırma veya değiştirme**. Uyarının neden oluştuğunu ve etkilerini anladıysanız, uyarıyı yoksayabilirsiniz.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK birden çok konuma yüklendi

**Yeni proje** iletişim kutusunda ASP.NET Core için aşağıdaki uyarıyı görebilirsiniz:

> .NET Core SDK birden çok konuma yüklenir. Yalnızca ' C: \\ Program Files DotNet SDK ' konumunda yüklü SDK 'lardan şablonlar \\ \\ \\ görüntülenir.

*C: \\ Program Files \\ DotNet \\ SDK \\ *dışında bir dizinde en az bir .NET Core SDK yüklemeniz olduğunda bu iletiyi görürsünüz. Genellikle bu, .NET Core SDK MSI yükleyicisi yerine Kopyala/Yapıştır kullanılarak bir makineye dağıtıldığında meydana gelir.

Bu uyarıyı engellemek için tüm 32-bit .NET Core SDK 'larını ve çalışma zamanlarını kaldırın. **Denetim Masası**  >  **Programlar ve Özellikler**'den Kaldır  >  **bir programı kaldırma veya değiştirme**. Uyarının neden oluştuğunu ve etkilerini anladıysanız, uyarıyı yoksayabilirsiniz.

### <a name="no-net-core-sdks-were-detected"></a>.NET Core SDK 'Ları algılanmadı

* ASP.NET Core için Visual Studio **Yeni proje** iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:

  > .NET Core SDK 'Ları algılanmadı, ortam değişkenine dahil olduklarından emin olun `PATH` .

* Bir komut yürütürken `dotnet` , uyarı şöyle görünür:

  > Yüklü olan DotNet SDK 'Ları bulmak mümkün değildi.

Bu uyarılar, ortam değişkeni `PATH` makinede herhangi bir .NET Core SDK 'sı üzerine işaret etmez görüntülenir. Bu sorunu çözmek için:

* .NET Core SDK 'i yükler. [.Net Indirmelerinde](https://dotnet.microsoft.com/download)en son yükleyiciyi edinin.
* `PATH`Ortam DEĞIŞKENININ SDK 'nın yüklü olduğu konuma işaret ettiğini doğrulayın ( `C:\Program Files\dotnet\` 64 bit/x64 veya `C:\Program Files (x86)\dotnet\` 32 bit/x86 için). SDK yükleyicisi normalde ' i ayarlar `PATH` . Aynı bit genişliği SDK 'larını ve çalışma zamanlarını aynı makineye her zaman yükler.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core barındırma paketi yüklendikten sonra SDK eksik

.NET Core [barındırma](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) paketinin yüklenmesi, .net core `PATH` 'un 32-bit (x86) sürümünü () işaret etmek üzere .NET Core çalışma zamanını yüklerken değiştirir `C:\Program Files (x86)\dotnet\` . Bu, 32-bit (x86) .NET Core `dotnet` komutu kullanıldığında ([.NET Core SDK 'ları algılanmadığında](#no-net-core-sdks-were-detected)) eksik SDK 'lara yol açabilir. Bu sorunu çözmek için, `C:\Program Files\dotnet\` öncesinde bir konuma geçin `C:\Program Files (x86)\dotnet\` `PATH` .

## <a name="obtain-data-from-an-app"></a>Uygulamadan veri alma

Bir uygulama isteklere yanıt veriyorsa, ara yazılım kullanarak uygulamadan aşağıdaki verileri alabilirsiniz:

* İstek: Yöntem, düzen, ana bilgisayar, pathbase, yol, sorgu dizesi, üst bilgiler
* Bağlantı: uzak IP adresi, uzak bağlantı noktası, yerel IP adresi, yerel bağlantı noktası, istemci sertifikası
* Identity: Name, display name
* Yapılandırma ayarları
* Ortam değişkenleri

Aşağıdaki [Ara yazılım](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) kodunu `Startup.Configure` metodun istek işleme işlem hattının başına yerleştirin. Bu ortam, kodun yalnızca geliştirme ortamında yürütülmesini sağlamak için, ara yazılım çalıştırılmadan önce denetlenir.

Ortamı edinmek için aşağıdaki yaklaşımlardan birini kullanın:

* `IHostingEnvironment` `Startup.Configure` Metodunu yöntemine ekleyin ve yerel değişkenle ortamı kontrol edin. Aşağıdaki örnek kodda bu yaklaşım gösterilmektedir.

* Ortamı sınıfındaki bir özelliğe atayın `Startup` . Özelliğini kullanarak ortamı denetleyin (örneğin, `if (Environment.IsDevelopment())` ).

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

## <a name="debug-aspnet-core-apps"></a>ASP.NET Core uygulamalarda hata ayıkla

Aşağıdaki bağlantılar ASP.NET Core uygulamalarda hata ayıklama hakkında bilgi sağlar.

* [Linux 'ta ASP Core hata ayıklaması](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [SSH üzerinde UNIX üzerinde .NET Core hata ayıklaması](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Hızlı başlangıç: Visual Studio hata ayıklayıcısı ile ASP.NET hata ayıklayın](/visualstudio/debugger/quickstart-debug-aspnet)
* Daha fazla hata ayıklama bilgisi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/2960) bakın.
