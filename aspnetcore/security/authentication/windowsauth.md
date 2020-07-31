---
title: ASP.NET Core 'da Windows kimlik doğrulamasını yapılandırma
author: scottaddie
description: IIS ve HTTP.sys için ASP.NET Core Windows kimlik doğrulamasını nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330696"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>ASP.NET Core 'da Windows kimlik doğrulamasını yapılandırma

[Scott Ade](https://twitter.com/Scott_Addie) tarafından

::: moniker range=">= aspnetcore-3.0"

Windows kimlik doğrulaması (Negotiate, Kerberos veya NTLM kimlik doğrulaması olarak da bilinir), [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)veya [HTTP.sys](xref:fundamentals/servers/httpsys)ile barındırılan ASP.NET Core uygulamalar için yapılandırılabilir.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Windows kimlik doğrulaması (Negotiate, Kerberos veya NTLM kimlik doğrulaması olarak da bilinir), [IIS](xref:host-and-deploy/iis/index) veya [HTTP.sys](xref:fundamentals/servers/httpsys)ile barındırılan ASP.NET Core uygulamalar için yapılandırılabilir.

::: moniker-end

Windows kimlik doğrulaması, ASP.NET Core uygulama kullanıcılarının kimliğini doğrulamak için işletim sistemini kullanır. Sunucunuz, kullanıcıları tanımlamak için Active Directory etki alanı kimliklerini veya Windows hesaplarını kullanarak bir kurumsal ağda çalıştığında Windows kimlik doğrulamasını kullanabilirsiniz. Windows kimlik doğrulaması, kullanıcıların, istemci uygulamalarının ve Web sunucularının aynı Windows etki alanına ait olduğu intranet ortamları için idealdir.

> [!NOTE]
> Windows kimlik doğrulaması, HTTP/2 ile desteklenmez. Kimlik doğrulaması sorunları HTTP/2 Yanıtlarına gönderilebilir, ancak kimlik doğrulamadan önce istemci HTTP/1.1 sürümüne indirgenmelidir.

## <a name="proxy-and-load-balancer-scenarios"></a>Proxy ve yük dengeleyici senaryoları

Windows kimlik doğrulaması, birincil olarak bir intranette kullanılan ve bir ara sunucu veya yük dengeleyicinin istemciler ve sunucular arasındaki trafiği genellikle işlemeyen bir durum bilgisi olan senaryodur. Bir ara sunucu veya yük dengeleyici kullanılırsa, Windows kimlik doğrulaması yalnızca proxy veya yük dengeleyici için geçerlidir:

* Kimlik doğrulamasını işler.
* Kullanıcı kimlik doğrulama bilgilerini uygulamaya geçirir (örneğin, bir istek üstbilgisinde), kimlik doğrulama bilgileri üzerinde davranır.

Proxy 'lerin ve yük dengeleyicilerin kullanıldığı ortamlarda Windows kimlik doğrulamasına alternatif olarak, OpenID Connect (OıDC) ile Federasyon Hizmetleri (ADFS) Active Directory.

## <a name="iisiis-express"></a>IIS/IIS Express

<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> İçinde (ad alanı) çağırarak kimlik doğrulama hizmetleri ekleyin `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Başlatma ayarları (hata ayıklayıcı)

Başlatma ayarları için yapılandırma yalnızca IIS Express dosya *üzerindeki özellikleri/launchSettings.js* etkiler ve Windows kimlik doğrulaması için IIS 'yi yapılandırmaz. Sunucu yapılandırması [IIS](#iis) bölümünde açıklanmıştır.

Visual Studio veya .NET Core CLI ile kullanılabilen **Web uygulaması** şablonu, Windows kimlik doğrulamasını destekleyecek şekilde yapılandırılabilir ve bu, dosyadaki *özellikleri/launchSettings.js* otomatik olarak güncelleştirir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Yeni proje**

1. Yeni bir proje oluşturma.
1. **ASP.NET Core Web uygulaması**' nı seçin. **İleri**’yi seçin.
1. **Proje adı** alanına bir ad girin. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur**’u seçin.
1. **Kimlik doğrulaması**altında **Değiştir** ' i seçin.
1. **Kimlik doğrulamasını Değiştir** penceresinde, **Windows kimlik doğrulaması**' nı seçin. **Tamam**’ı seçin.
1. **Web uygulaması**' nı seçin.
1. **Oluştur**’u seçin.

Uygulamayı çalıştırın. Kullanıcı adı, işlenmiş uygulamanın kullanıcı arabiriminde görüntülenir.

**Mevcut proje**

Projenin özellikleri Windows kimlik doğrulamasını etkinleştirir ve anonim kimlik doğrulamasını devre dışı bırakır:

1. **Çözüm Gezgini** ' de projeye sağ tıklayın ve **Özellikler**' i seçin.
1. **Hata Ayıkla** sekmesini seçin.
1. **Anonim kimlik doğrulamasını etkinleştir**onay kutusunun işaretini kaldırın.
1. **Windows kimlik doğrulamasını etkinleştir**onay kutusunu işaretleyin.
1. Özellik sayfasını kaydedin ve kapatın.

Alternatif olarak, Özellikler `iisSettings` dosyadaki *launchSettings.js* düğümünde yapılandırılabilir:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**Yeni proje**

Bağımsız değişkenle [DotNet New](/dotnet/core/tools/dotnet-new) komutunu yürütün `webapp` (ASP.NET Core Web uygulaması) ve `--auth Windows` anahtar:

```dotnetcli
dotnet new webapp --auth Windows
```

**Mevcut proje**

`iisSettings`Dosyadaki *launchSettings.js* düğümünü güncelleştirin:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Mevcut bir projeyi değiştirirken, proje dosyasının [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) **veya** [Microsoft. aspnetcore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet paketi için bir paket başvurusu içerdiğini doğrulayın.

### <a name="iis"></a>IIS

IIS, ASP.NET Core uygulamalarını barındırmak için [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) kullanır. Windows kimlik doğrulaması, *web.config* dosyası aracılığıyla IIS için yapılandırılır. Aşağıdaki bölümlerde aşağıdakilerin nasıl yapılacağı gösterilmektedir:

* Uygulama dağıtıldığında sunucuda Windows kimlik doğrulamasını etkinleştiren bir yerel *web.config* dosyası sağlayın.
* Sunucuda zaten dağıtılan bir ASP.NET Core uygulamasının *web.config* dosyasını YAPıLANDıRMAK Için IIS Yöneticisi 'ni kullanın.

Daha önce yapmadıysanız, IIS 'yi ASP.NET Core uygulamaları barındıracak şekilde etkinleştirin. Daha fazla bilgi için bkz. <xref:host-and-deploy/iis/index>.

Windows kimlik doğrulaması için IIS rol hizmetini etkinleştirin. Daha fazla bilgi için bkz. [IIS rol hizmetlerinde Windows kimlik doğrulamasını etkinleştirme (bkz. 2. adım)](xref:host-and-deploy/iis/index#iis-configuration).

[IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , varsayılan olarak isteklerin kimliğini otomatik olarak doğrulamak için yapılandırılır. Daha fazla bilgi için bkz. [IIS Ile Windows üzerinde ana bilgisayar ASP.NET Core: IIS seçenekleri (Automatıcauthentication)](xref:host-and-deploy/iis/index#iis-options).

ASP.NET Core modülü, Windows kimlik doğrulama belirtecini varsayılan olarak uygulamaya iletecek şekilde yapılandırılmıştır. Daha fazla bilgi için bkz. [ASP.NET Core modülü yapılandırma başvurusu: aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Aşağıdaki yaklaşımlardan **birini** kullanın:

* **Projeyi yayımlamadan ve dağıtmaya başlamadan önce** aşağıdaki *web.config* dosyasını proje köküne ekleyin:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Proje, .NET Core SDK ( `<IsTransformWebConfigDisabled>` Proje dosyasında özelliği olarak ayarlanmış olmayan) tarafından yayımlandığında `true` , yayımlanan *web.config* dosyası `<location><system.webServer><security><authentication>` bölümünü içerir. Özelliği hakkında daha fazla bilgi için `<IsTransformWebConfigDisabled>` bkz <xref:host-and-deploy/iis/index#webconfig-file> ..

* **Projeyi yayımladıktan ve dağıttıktan sonra** IIS yöneticisiyle sunucu tarafı yapılandırması gerçekleştirin:

  1. IIS Yöneticisi 'nde, **Bağlantılar** kenar çubuğu ' nun **sıteler** düğümü altında IIS sitesini seçin.
  1. **IIS** alanında **kimlik doğrulaması** ' na çift tıklayın.
  1. **Anonim kimlik doğrulamasını**seçin. **Eylemler** kenar çubuğunda **devre dışı bırak** ' ı seçin.
  1. **Windows kimlik doğrulaması**' nı seçin. **Eylemler** kenar çubuğunda **Etkinleştir** ' i seçin.

  Bu eylemler çekilirken, IIS Yöneticisi uygulamanın *web.config* dosyasını değiştirir. `<system.webServer><security><authentication>`Ve için güncelleştirilmiş ayarlarla bir düğüm eklenir `anonymousAuthentication` `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`IIS Yöneticisi tarafından *web.config* dosyasına eklenen bölüm, `<location>` uygulama yayımlandığında .NET Core SDK tarafından eklenen uygulama bölümünün dışındadır. Bölüm düğümün dışına eklendiğinden `<location>` , ayarlar herhangi bir [alt uygulama](xref:host-and-deploy/iis/index#sub-applications) tarafından geçerli uygulamaya devralınır. Devralmayı önlemek için, eklenen `<security>` bölümü `<location><system.webServer>` .NET Core SDK bölümün içine taşıyın.

  IIS Yöneticisi IIS yapılandırmasını eklemek için kullanıldığında, yalnızca sunucudaki *web.config* dosyasını etkiler. Sunucunun *web.config* kopyası projenin *web.config* dosyası tarafından değiştirilirse uygulamanın sonraki dağıtımı sunucudaki ayarların üzerine yazabilir. Ayarları yönetmek için aşağıdaki yaklaşımlardan **birini** kullanın:

  * Dağıtım sırasında dosyanın üzerine yazıldıktan sonra *web.config* dosyasındaki ayarları SıFıRLAMAK Için IIS Yöneticisi 'ni kullanın.
  * Ayarlarla yerel olarak uygulamaya bir *web.config dosyası* ekleyin.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Windows, Linux ve macOS 'ta Negotiate ve Kerberos kullanarak Windows kimlik doğrulamasını desteklemek için [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet paketi [Kestrel](xref:fundamentals/servers/kestrel) ile birlikte kullanılabilir.

> [!WARNING]
> Kimlik bilgileri bir bağlantı üzerindeki istekler arasında kalıcı olabilir. *Proxy, Kestrel ile bir 1:1 bağlantı benzeşimi (kalıcı bağlantı) tutmadığı müddetçe, Negotiate kimlik doğrulaması proxy ile kullanılmamalıdır.*

> [!NOTE]
> Negotiate işleyicisi, temeldeki sunucunun Windows kimlik doğrulamasını yerel olarak destekleyip desteklemediğini ve etkinleştirilip etkinleştirilmediğini algılar. Sunucu Windows kimlik doğrulamasını destekliyorsa, ancak devre dışıysa sunucu uygulamasını etkinleştirmenizi isteyen bir hata oluşur. Sunucuda Windows kimlik doğrulaması etkinleştirildiğinde, anlaşma işleyicisi saydam olarak buna iletilir.

Ve ' i çağırarak kimlik doğrulama hizmetleri ekleyin <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Şunları çağırarak kimlik doğrulama ara yazılımı ekleyin <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Ara yazılım hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..

Anonim isteklere izin verilir. Kimlik doğrulaması için anonim istekleri zorluk [ASP.NET Core yetkilendirmeyi](xref:security/authorization/introduction) kullanın.

### <a name="windows-environment-configuration"></a>Windows ortam yapılandırması

[Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) bileşeni, Kullanıcı modu kimlik doğrulaması gerçekleştirir. Hizmet sorumlusu adları (SPN 'Ler) makine hesabını değil, hizmeti çalıştıran kullanıcı hesabına eklenmelidir. `setspn -S HTTP/myservername.mydomain.com myuser`Yönetim komut kabuğu 'nda yürütün.

### <a name="linux-and-macos-environment-configuration"></a>Linux ve macOS ortam yapılandırması

Linux veya macOS makinesini bir Windows etki alanına katılmaya yönelik yönergeler, [Windows kimlik doğrulaması-Kerberos makalesini kullanarak SQL Server Azure Data Studio bağlanma](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) ' da kullanılabilir. Yönergeler, etki alanındaki Linux makinesi için bir makine hesabı oluşturur. SPN 'Ler bu makine hesabına eklenmelidir.

> [!NOTE]
> [Windows kimlik doğrulaması-Kerberos makalesini kullanarak SQL Server Azure Data Studio bağlanma](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) ' daki kılavuzdan sonra, `python-software-properties` gerekirse öğesini ile değiştirin `python3-software-properties` .

Linux veya macOS makinesi etki alanına katıldığında, SPN 'Ler içeren bir [keytab dosyası](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) sağlamak için ek adımlar gerekir:

* Etki alanı denetleyicisinde, makine hesabına yeni Web hizmeti SPN 'Leri ekleyin:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Bir keytab dosyası oluşturmak için [Ktpass](/windows-server/administration/windows-commands/ktpass) kullanın:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Bazı alanların gösterildiği gibi büyük harfle belirtilmesi gerekir.
* Keytab dosyasını Linux veya macOS makinesine kopyalayın.
* Bir ortam değişkeni aracılığıyla keytab dosyasını seçin:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* `klist`Şu anda kullanılabilecek SPN 'leri göstermek için çağırın.

> [!NOTE]
> Bir keytab dosyası, etki alanı erişimi kimlik bilgilerini içerir ve buna uygun şekilde korunması gerekir.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) , NEGOTIATE, NTLM veya temel kimlik doğrulaması kullanarak çekirdek modu Windows kimlik doğrulamasını destekler.

<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> İçinde (ad alanı) çağırarak kimlik doğrulama hizmetleri ekleyin `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Uygulamanın Web konağını, Windows kimlik doğrulaması (*program.cs*) ile HTTP.sys kullanacak şekilde yapılandırın. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*><xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>ad alanında.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys. Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez. Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır. Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.

> [!NOTE]
> HTTP.sys nano sunucu 1709 veya sonraki bir sürümde desteklenmez. Windows kimlik doğrulaması 'nı ve nano sunucu ile HTTP.sys kullanmak için, bir [sunucu çekirdeği (Microsoft/windowsservercore) kapsayıcısı](https://hub.docker.com/r/microsoft/windowsservercore/)kullanın. Sunucu Çekirdeği hakkında daha fazla bilgi için bkz. [Windows Server 'Da Sunucu Çekirdeği yükleme seçeneği nedir?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Kullanıcıları yetkilendir

Anonim erişimin yapılandırma durumu, `[Authorize]` ve `[AllowAnonymous]` özniteliklerinin uygulamada kullanılma şeklini belirler. Aşağıdaki iki bölümde, anonim erişimin izin verilmeyen ve izin verilen yapılandırma durumlarının nasıl işleneceği açıklanmaktadır.

### <a name="disallow-anonymous-access"></a>Anonim erişime izin verme

Windows kimlik doğrulaması etkinleştirildiğinde ve anonim erişim devre dışı bırakıldığında, `[Authorize]` ve `[AllowAnonymous]` özniteliklerinin etkisi yoktur. Bir IIS sitesi anonim erişime izin vermeyecek şekilde yapılandırıldıysa, istek uygulamaya hiçbir şekilde ulaşmaz. Bu nedenle, `[AllowAnonymous]` öznitelik geçerli değildir.

### <a name="allow-anonymous-access"></a>Anonim erişime izin ver

Hem Windows kimlik doğrulaması hem de anonim erişim etkinleştirildiğinde, `[Authorize]` ve özniteliklerini kullanın `[AllowAnonymous]` . `[Authorize]`Özniteliği, uygulamanın kimlik doğrulaması gerektiren uç noktaların güvenliğini sağlamanıza olanak tanır. `[AllowAnonymous]`Özniteliği, `[Authorize]` anonim erişime izin veren uygulamalardaki özniteliği geçersiz kılar. Öznitelik kullanım ayrıntıları için bkz <xref:security/authorization/simple> ..

> [!NOTE]
> Varsayılan olarak, bir sayfaya erişim yetkisi olmayan kullanıcılara boş bir HTTP 403 yanıtı sunulur. [Statuscodepages ara yazılımı](xref:fundamentals/error-handling#usestatuscodepages) , kullanıcılara daha iyi bir "erişim reddedildi" deneyimi sunacak şekilde yapılandırılabilir.

## <a name="impersonation"></a>Kimliğe bürünme

ASP.NET Core kimliğe bürünme uygulamaz. Uygulamalar, uygulama havuzu veya işlem kimliği kullanarak tüm istekler için uygulamanın kimliğiyle çalışır. Uygulamanın bir kullanıcı adına bir eylem gerçekleştirmesi gerekiyorsa, içindeki bir [Terminal satır içi ara yazılım](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) Içinde [WindowsIdentity. runınkimliğine](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) bürünme kullanın `Startup.Configure` . Bu bağlamda tek bir eylem çalıştırın ve sonra bağlamı kapatın.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`, zaman uyumsuz işlemleri desteklemez ve karmaşık senaryolar için kullanılmamalıdır. Örneğin, tüm isteklerin veya ara yazılım zincirlerinin sarmalanması desteklenmez veya önerilmez.

::: moniker range=">= aspnetcore-3.0"

[Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi Windows, Linux ve MacOS 'ta kimlik doğrulamaya izin sağlarken, kimliğe bürünme yalnızca Windows 'da desteklenir.

::: moniker-end

## <a name="claims-transformations"></a>Talep dönüşümleri

::: moniker range=">= aspnetcore-3.0"

IIS ile barındırırken, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz. Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez. Daha fazla bilgi ve talep dönüştürmelerini etkinleştiren bir kod örneği için bkz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> ..

::: moniker-end

::: moniker range="< aspnetcore-3.0"

IIS işlem içi modunda barındırırken, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz. Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez. İşlem içinde barındırırken talep dönüştürmelerini etkinleştiren daha fazla bilgi ve kod örneği için bkz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> ..

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
