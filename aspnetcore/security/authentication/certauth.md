---
title: ASP.NET Core sertifika kimlik doğrulamasını yapılandırma
author: blowdart
description: IIS ve HTTP.sys için ASP.NET Core sertifika kimlik doğrulamasını nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/certauth
ms.openlocfilehash: 7a23f2b17cc8fb3a4989b9fddd5c128add13db5b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021958"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>ASP.NET Core sertifika kimlik doğrulamasını yapılandırma

`Microsoft.AspNetCore.Authentication.Certificate`ASP.NET Core için [sertifika kimlik doğrulamasına](https://tools.ietf.org/html/rfc5246#section-7.4.4) benzer bir uygulama içerir. Sertifika kimlik doğrulaması TLS düzeyinde gerçekleşir ve bu süre ASP.NET Core. Daha doğru, bu, sertifikayı doğrulayan bir kimlik doğrulama işleyicisidir ve bu sertifikayı bir ' a çözebileceğiniz bir olay verir `ClaimsPrincipal` . 

[Sunucunuzu](#configure-your-server-to-require-certificates) sertifika kimlik doğrulaması için yapılandırın, BT IIS, Kestrel, Azure Web Apps veya kullandığınız başka herhangi bir şeydir.

## <a name="proxy-and-load-balancer-scenarios"></a>Proxy ve yük dengeleyici senaryoları

Sertifika kimlik doğrulaması, genellikle bir ara sunucu veya yük dengeleyicinin istemciler ve sunucular arasındaki trafiği işleyememesi durumunda kullanılan, durum bilgisi olan bir senaryodur Bir ara sunucu veya yük dengeleyici kullanılıyorsa, sertifika kimlik doğrulaması yalnızca proxy veya yük dengeleyici için geçerlidir:

* Kimlik doğrulamasını işler.
* Kullanıcı kimlik doğrulama bilgilerini uygulamaya geçirir (örneğin, bir istek üstbilgisinde), kimlik doğrulama bilgileri üzerinde davranır.

Proxy 'lerin ve yük dengeleyicilerin kullanıldığı ortamlarda sertifika kimlik doğrulamasına alternatif olarak, OpenID Connect (OıDC) ile Federasyon Hizmetleri (ADFS) Active Directory.

## <a name="get-started"></a>başlarken

HTTPS sertifikası alın, uygulayın ve sunucunuzu sertifika gerektirecek şekilde [yapılandırın](#configure-your-server-to-require-certificates) .

Web uygulamanızda, [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) paketine bir başvuru ekleyin. Daha sonra `Startup.ConfigureServices` yönteminde, isteklerle birlikte `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` `OnCertificateValidated` gönderilen istemci sertifikasında herhangi bir destek doğrulaması yapmak için bir temsilci sağlayan seçeneklerinizde çağrı yapın. Bu bilgileri bir öğesine dönüştürün `ClaimsPrincipal` ve özelliği üzerinde ayarlayın `context.Principal` .

Kimlik doğrulaması başarısız olursa, bu işleyici `403 (Forbidden)` `401 (Unauthorized)` , bekleolabileceğiniz gibi bir yanıt döndürür. Bu durum, kimlik doğrulamanın ilk TLS bağlantısı sırasında gerçekleşme nedendir. İşleyiciye ulaştığında, çok geç olur. Anonim bir bağlantıyla bir sertifikayla bir bağlantıyı yükseltmenin bir yolu yoktur.

Yöntemine de ekleyin `app.UseAuthentication();` `Startup.Configure` . Aksi takdirde, `HttpContext.User` sertifika, sertifikadan oluşturulacak şekilde ayarlanmayacak `ClaimsPrincipal` . Örnek:

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

Önceki örnekte sertifika kimlik doğrulaması eklemenin varsayılan yolu gösterilmektedir. İşleyici, ortak sertifika özelliklerini kullanarak bir Kullanıcı sorumlusu oluşturur.

## <a name="configure-certificate-validation"></a>Sertifika doğrulamasını yapılandırma

`CertificateAuthenticationOptions`İşleyicide, bir sertifikada gerçekleştirmeniz gereken en düşük doğrulamalar olan yerleşik doğrulamalar vardır. Bu ayarların her biri varsayılan olarak etkindir.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = zincirleme, SelfSigned veya All (zincirleme | SelfSigned)

Varsayılan değer:`CertificateTypes.Chained`

Bu denetim yalnızca uygun sertifika türüne izin verildiğini doğrular. Uygulama otomatik olarak imzalanan sertifikalar kullanıyorsa, bu seçeneğin veya olarak ayarlanması gerekir `CertificateTypes.All` `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Varsayılan değer:`true`

Bu denetim, istemci tarafından sunulan sertifikanın Istemci kimlik doğrulaması genişletilmiş anahtar kullanımı (EKU) olduğunu veya hiç EKU olmadığını doğrular. Belirtimlerde bir EKU belirtilmemişse, tüm EKU 'lar geçerli kabul edilir.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Varsayılan değer:`true`

Bu denetim, sertifikanın geçerlilik süresi içinde olduğunu doğrular. Her istekte işleyici, geçerli oturumu sırasında sunulmadığı zaman geçerli olmayan bir sertifikanın süresinin dolmamasını sağlar.

### <a name="revocationflag"></a>Revocationbayrağı

Varsayılan değer:`X509RevocationFlag.ExcludeRoot`

Zincirdeki hangi sertifikaların iptal için denetleneceğini belirten bayrak.

İptal denetimleri yalnızca sertifika bir kök sertifikaya zincirleme yapıldığında gerçekleştirilir.

### <a name="revocationmode"></a>Revocationmodu

Varsayılan değer:`X509RevocationMode.Online`

İptal denetimlerinin nasıl gerçekleştirileceğini belirten bayrak.

Bir çevrimiçi denetim belirtildiğinde, sertifika yetkilisi ile bağlantı kurulduğunda uzun bir gecikmeyle sonuçlanabilir.

İptal denetimleri yalnızca sertifika bir kök sertifikaya zincirleme yapıldığında gerçekleştirilir.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Uygulamamı yalnızca belirli yollarda sertifika gerektirecek şekilde yapılandırabilir miyim?

Bu mümkün değildir. Sertifika değişimi 'nin HTTPS konuşması başlangıcı olduğunu unutmayın, bu bağlantı üzerinde ilk istek alınmadan önce sunucu tarafından gerçekleştirilir, böylece herhangi bir istek alanı temelinde kapsam yapılamaz.

## <a name="handler-events"></a>İşleyici olayları

İşleyicinin iki olayı vardır:

* `OnAuthenticationFailed`: Kimlik doğrulaması sırasında bir özel durum oluşursa ve tepki vermenize izin veriyorsa çağırılır.
* `OnCertificateValidated`: Sertifika doğrulandıktan sonra, doğrulama geçildi ve bir varsayılan asıl oluşturulur. Bu olay kendi doğrulamayı gerçekleştirmenize ve sorumluyu artırabilir veya değiştirmenize olanak sağlar. Örnekler için şunları içerir:
  * Sertifikanın hizmetlerinize göre bilinip tanınmadığını belirleme.
  * Kendi sorumlunuzu oluşturma. İçinde aşağıdaki örneği göz önünde bulundurun `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

Gelen sertifikayı, ek doğrulamadan uymadığını fark ederseniz `context.Fail("failure reason")` bir hata nedeniyle çağırın.

Gerçek işlevsellik için muhtemelen bir veritabanına veya diğer Kullanıcı Mağazası türüne bağlanan bağımlılık ekleme bölümünde kayıtlı bir hizmeti çağırmak isteyeceksiniz. Temsilciniz 'e geçirilen bağlamı kullanarak hizmetinize erişin. İçinde aşağıdaki örneği göz önünde bulundurun `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

Kavramsal olarak, sertifikanın doğrulanması bir yetkilendirme konusudur. Örneğin, bir yetkilendirme ilkesindeki bir veren veya parmak izi gibi bir denetim eklemek, `OnCertificateValidated` mükemmel bir kabul edilebilir.

## <a name="configure-your-server-to-require-certificates"></a>Sunucunuzu sertifika gerektirecek şekilde yapılandırma

### <a name="kestrel"></a>Kestrel

*Program.cs*' de, Kestrel ' yi aşağıdaki şekilde yapılandırın:

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.

### <a name="iis"></a>IIS

IIS Yöneticisi 'nde aşağıdaki adımları uygulayın:

1. **Bağlantılar** sekmesinden sitenizi seçin.
1. **Özellikler Görünümü** penceresinde **SSL ayarları** seçeneğine çift tıklayın.
1. **SSL gerektir** onay kutusunu Işaretleyin ve **istemci sertifikaları** bölümünde radyo **iste** düğmesini seçin.

![IIS 'de istemci sertifikası ayarları](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure ve özel Web proxy 'leri

Sertifika iletme ara yazılımını yapılandırma hakkında bilgi için bkz. [konak ve dağıtım belgeleri](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Azure Web Apps sertifika kimlik doğrulamasını kullanma

Azure için bir iletme yapılandırması gerekmez. Bu, sertifika iletme ara ortamında zaten kuruldu.

> [!NOTE]
> Bu, Certificateforwardingara yazılımı 'nın mevcut olmasını gerektirir.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Özel Web proxy 'lerinde sertifika kimlik doğrulamasını kullanma

`AddCertificateForwarding`Yöntemi belirtmek için kullanılır:

* İstemci üst bilgi adı.
* Sertifika nasıl yüklenir ( `HeaderConverter` özelliğini kullanarak).

Özel Web proxy 'lerinde, sertifika özel bir istek üst bilgisi olarak geçirilir, örneğin `X-SSL-CERT` . Bunu kullanmak için, içinde sertifika iletmeyi yapılandırın `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

`Startup.Configure`Yöntemi daha sonra ara yazılımı ekler. `UseCertificateForwarding`, ve çağrıları için çağrılır `UseAuthentication` `UseAuthorization` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Ayrı bir sınıf, doğrulama mantığını uygulamak için kullanılabilir. Bu örnekte otomatik olarak imzalanan sertifika kullanıldığından, yalnızca sertifikanızın kullanılabildiğinden emin olun. İstemci sertifikasının ve sunucu sertifikasının parmak izlerinin eşleştiğinden emin olun, aksi takdirde herhangi bir sertifika kullanılabilir ve kimlik doğrulaması için yeterli olacaktır. Bu, yöntemi içinde kullanılacaktır `AddCertificate` . Ara veya alt sertifikalar kullanıyorsanız konuyu veya sertifikayı vereni de doğrulayabilirsiniz.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Sertifika ve HttpClientHandler kullanarak bir HttpClient uygulama

, `HttpClientHandler` Sınıfının oluşturucusuna doğrudan eklenebilir `HttpClient` . Örnekleri oluşturulurken dikkatli olunmalıdır `HttpClient` . `HttpClient`Ardından, her istek ile sertifikayı gönderir.

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Bir sertifika ve ıhttpclientfactory adlı bir HttpClient kullanarak bir HttpClient uygulama 

Aşağıdaki örnekte, bir istemci sertifikası, `HttpClientHandler` işleyicisinden özelliği kullanılarak bir öğesine eklenir `ClientCertificates` . Bu işleyici daha sonra yöntemi kullanılarak bir adlandırılmış örneğinde kullanılabilir `HttpClient` `ConfigurePrimaryHttpMessageHandler` . Bu kurulum `Startup.ConfigureServices` :

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

`IHttpClientFactory`Daha sonra, adlandırılmış örneği işleyicinin ve sertifikayla almak için kullanılabilir. `CreateClient`Sınıfında tanımlanan istemci adına sahip Yöntem `Startup` örneği almak için kullanılır. HTTP isteği, gereken şekilde istemci kullanılarak gönderilebilir.

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

Sunucuya doğru sertifika gönderiliyorsa, veriler döndürülür. Sertifika yoksa veya yanlış sertifika gönderilmezse bir HTTP 403 durum kodu döndürülür.

### <a name="create-certificates-in-powershell"></a>PowerShell 'de sertifika oluşturma

Sertifikaların oluşturulması, bu akışı ayarlamanın en zor bölümüdür. Bir kök sertifika, `New-SelfSignedCertificate` PowerShell cmdlet 'i kullanılarak oluşturulabilir. Sertifikayı oluştururken güçlü bir parola kullanın. `KeyUsageProperty`Parametresi ve `KeyUsage` parametresini gösterildiği gibi eklemek önemlidir.

#### <a name="create-root-ca"></a>Kök CA oluştur

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> `-DnsName`Parametre değeri uygulamanın dağıtım hedefi ile aynı olmalıdır. Örneğin, geliştirme için "localhost".

#### <a name="install-in-the-trusted-root"></a>Güvenilen köke yüklensin

Kök sertifikanın ana bilgisayar sisteminizde güvenilir olması gerekir. Sertifika yetkilisi tarafından oluşturulmamış bir kök sertifikaya varsayılan olarak güvenilmez. Aşağıdaki bağlantıda bunun Windows 'da nasıl gerçekleştirebileceği açıklanmaktadır:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Ara sertifika

Artık kök sertifikadan bir ara sertifika oluşturulabilir. Bu, tüm kullanım durumları için gerekli değildir, ancak birçok sertifika oluşturmanız veya sertifika gruplarını etkinleştirmeniz veya devre dışı bırakmanız gerekebilir. `TextExtension`Sertifikanın temel kısıtlamalarında yol uzunluğunu ayarlamak için parametresi gereklidir.

Ara sertifika daha sonra Windows ana bilgisayar sistemindeki güvenilen ara sertifikaya eklenebilir.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Ara sertifikadan alt sertifika oluştur

Ara sertifikadan bir alt sertifika oluşturulabilir. Bu, son varlıktır ve daha fazla alt sertifika oluşturmak zorunda değildir.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Kök sertifikadan alt sertifika oluştur

Kök sertifikadan doğrudan bir alt sertifika da oluşturulabilir. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Örnek kök-ara sertifika-sertifika

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

Kök, ara veya alt sertifikaları kullanırken, sertifikalar, Parmak Izi veya PublicKey kullanılarak gerektiği şekilde doğrulanabilir.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>Sertifika doğrulama önbelleği

ASP.NET Core 5,0 ve sonraki sürümler, doğrulama sonuçlarının önbelleğe alınmasını etkinleştirme yeteneğini destekler. Doğrulama pahalı bir işlem olduğundan, önbelleğe alma, sertifika kimlik doğrulamasının performansını önemli ölçüde artırır.

Sertifika kimlik doğrulaması varsayılan olarak önbelleğe almayı devre dışı bırakır. Önbelleğe almayı etkinleştirmek için şunu `AddCertificateCache` arayın `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

Varsayılan önbelleğe alma uygulama sonuçları bellekte depolar. Bağımlılık ekleme ile kaydederek kendi önbelleğinizi sağlayabilirsiniz `ICertificateValidationCache` . Örneğin, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>İsteğe bağlı istemci sertifikaları

Bu bölüm, bir sertifika ile uygulamanın bir alt kümesini koruması gereken uygulamalar için bilgiler sağlar. Örneğin, uygulamadaki bir Razor sayfa veya denetleyici istemci sertifikaları gerektirebilir. Bu, zorlukları istemci sertifikaları olarak gösterir:
  
* , HTTP özelliği değil, bir TLS özelliğidir.
* , Bağlantı başına anlaşma yapılır ve herhangi bir HTTP verisi kullanılabilir olmadan önce bağlantının başlangıcında anlaşılıp verilmelidir. Bağlantının başlangıcında yalnızca Sunucu Adı Belirtme (SNı) &dagger; bilinirdi. İstemci ve sunucu sertifikaları, bir bağlantı üzerindeki ilk istekten önce görüşülür ve istekler genellikle yeniden anlaşma yapamaz.

TLS yeniden anlaşması, isteğe bağlı istemci sertifikaları uygulamak için eski bir yoldur. Bu artık önerilmez, çünkü:
- HTTP/1.1 ' de, bir POST isteği sırasında yeniden anlaşma, istek gövdesinin TCP penceresini doldurduğu ve yeniden anlaşma paketlerinin alınmamasına neden olabilir.
- HTTP/2 [açıkça](https://tools.ietf.org/html/rfc7540#section-9.2.1) yeniden anlaşmaya izin vermez.
- TLS 1,3, yeniden anlaşma desteğini [kaldırdı](https://tools.ietf.org/html/rfc8740#section-1) .

ASP.NET Core 5 Preview 7 ve üzeri, isteğe bağlı istemci sertifikaları için daha kolay destek ekler. Daha fazla bilgi için bkz. [Isteğe bağlı sertifikalar örneği](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

Aşağıdaki yaklaşım isteğe bağlı istemci sertifikalarını destekler:

* Etki alanı ve alt etki alanı için bağlamayı ayarlama:
  * Örneğin, ve üzerinde bağlamaları ayarlayın `contoso.com` `myClient.contoso.com` . `contoso.com`Konakta istemci sertifikası gerekmez `myClient.contoso.com` , ancak bunu yapar.
  * Daha fazla bilgi için bkz.
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions. UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Not Kestrel Şu anda tek bir bağlamada birden fazla TLS yapılandırmasını desteklemez, benzersiz IP 'Ler veya bağlantı noktalarıyla iki bağlama gerekecektir. Bakýnhttps://github.com/dotnet/runtime/issues/31097
    * IIS
      * [IIS barındırma](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [IIS 'de güvenliği yapılandırma](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys: [Windows Server 'ı yapılandırma](xref:fundamentals/servers/httpsys#configure-windows-server)
* İstemci sertifikası gerektiren ve bir tane olmayan Web uygulamasına yönelik istekler için:
  * İstemci sertifikası korumalı alt etki alanını kullanarak aynı sayfaya yönlendirin.
  * Örneğin, öğesine yönlendirin `myClient.contoso.com/requestedPage` . İsteği `myClient.contoso.com/requestedPage` öğesinden farklı bir ana bilgisayar adı olduğundan `contoso.com/requestedPage` , istemci farklı bir bağlantı oluşturur ve istemci sertifikası sağlanır.
  * Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.

[Bu GitHub tartışma](https://github.com/dotnet/AspNetCore.Docs/issues/18720) sorununa yönelik isteğe bağlı istemci sertifikalarıyla ilgili soruları, açıklamaları ve diğer geri bildirimleri bırakın.

&dagger;Sunucu Adı Belirtme (SNı), SSL anlaşmasının bir parçası olarak sanal etki alanı dahil etmek için bir TLS uzantısıdır. Bu, sanal etki alanı adının veya ana bilgisayar adının ağ uç noktasını tanımlamak için kullanılabileceği anlamına gelir.
