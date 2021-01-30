---
title: ASP.NET Core Kestrel Web sunucusu için uç noktaları yapılandırma
author: rick-anderson
description: ASP.NET Core için platformlar arası Web sunucusu olan Kestrel ile uç noktaları yapılandırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: f9d82409f4b31a5564c7cdfa48beb303d784e213
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057154"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="fbf2b-103">ASP.NET Core Kestrel Web sunucusu için uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="fbf2b-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="fbf2b-104">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="fbf2b-105">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="fbf2b-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="fbf2b-106">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-106">Specify URLs using the:</span></span>

* <span data-ttu-id="fbf2b-107">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="fbf2b-108">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="fbf2b-109">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="fbf2b-110">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="fbf2b-111">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="fbf2b-112">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="fbf2b-113">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="fbf2b-114">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-114">A development certificate is created:</span></span>

* <span data-ttu-id="fbf2b-115">[.NET SDK](/dotnet/core/sdk) yüklü olduğunda.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="fbf2b-116">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="fbf2b-117">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="fbf2b-118">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fbf2b-119"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="fbf2b-120">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="fbf2b-121">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="fbf2b-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="fbf2b-122">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="fbf2b-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="fbf2b-123">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="fbf2b-124">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="fbf2b-125">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="fbf2b-126">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="fbf2b-126">Configure(IConfiguration)</span></span>

<span data-ttu-id="fbf2b-127">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-127">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="fbf2b-128">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-128">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="fbf2b-129">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-129">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="fbf2b-130">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="fbf2b-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="fbf2b-131">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-131">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="fbf2b-132">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-132">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="fbf2b-133">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-133">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="listenoptionsusehttps"></a><span data-ttu-id="fbf2b-134">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="fbf2b-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="fbf2b-135">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="fbf2b-136">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="fbf2b-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="fbf2b-137">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="fbf2b-138">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-138">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="fbf2b-139">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="fbf2b-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="fbf2b-140">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="fbf2b-141">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="fbf2b-142">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="fbf2b-143">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="fbf2b-144">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="fbf2b-145">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="fbf2b-146">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="fbf2b-147">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="fbf2b-148">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="fbf2b-149">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="fbf2b-150">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="fbf2b-151">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-151">Supported configurations described next:</span></span>

* <span data-ttu-id="fbf2b-152">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="fbf2b-152">No configuration</span></span>
* <span data-ttu-id="fbf2b-153">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="fbf2b-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="fbf2b-154">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="fbf2b-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="fbf2b-155">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="fbf2b-155">No configuration</span></span>

<span data-ttu-id="fbf2b-156">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="fbf2b-157">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="fbf2b-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="fbf2b-158">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="fbf2b-159">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="fbf2b-160">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="fbf2b-161">`AllowInvalid` `true` Geçersiz sertifikaların kullanılmasına (örneğin, otomatik olarak imzalanan sertifikalar) izin vermek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="fbf2b-162">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası ( `HttpsDefaultCert` Aşağıdaki örnekte), altında veya geliştirme sertifikasıyla tanımlanan sertifikaya geri döner `Certificates:Default` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates:Default` or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="fbf2b-163">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-163">Schema notes:</span></span>

* <span data-ttu-id="fbf2b-164">Uç nokta adları [büyük/küçük harfe duyarlıdır](xref:fundamentals/configuration/index#configuration-keys-and-values).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-164">Endpoints names are [case-insensitive](xref:fundamentals/configuration/index#configuration-keys-and-values).</span></span> <span data-ttu-id="fbf2b-165">Örneğin, `HTTPS` ve `Https` eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-165">For example, `HTTPS` and `Https` are equivalent.</span></span>
* <span data-ttu-id="fbf2b-166">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-166">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="fbf2b-167">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-167">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="fbf2b-168">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-168">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="fbf2b-169">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-169">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="fbf2b-170">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-170">The `Certificate` section is optional.</span></span> <span data-ttu-id="fbf2b-171">`Certificate`Bölüm belirtilmemişse, içinde tanımlanan varsayılanlar `Certificates:Default` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-171">If the `Certificate` section isn't specified, the defaults defined in `Certificates:Default` are used.</span></span> <span data-ttu-id="fbf2b-172">Kullanılabilir varsayılan değer yoksa, geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-172">If no defaults are available, the development certificate is used.</span></span> <span data-ttu-id="fbf2b-173">Varsayılan değer yoksa ve geliştirme sertifikası yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-173">If there are no defaults and the development certificate isn't present, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="fbf2b-174">`Certificate`Bölüm birden çok [sertifika kaynağını](#certificate-sources)destekler.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-174">The `Certificate` section supports multiple [certificate sources](#certificate-sources).</span></span>
* <span data-ttu-id="fbf2b-175">Bağlantı noktası çakışmalarına neden olmadıkları sürece [yapılandırma](xref:fundamentals/configuration/index) bölümünde herhangi bir sayıda uç nokta tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-175">Any number of endpoints may be defined in [Configuration](xref:fundamentals/configuration/index) as long as they don't cause port conflicts.</span></span>

#### <a name="certificate-sources"></a><span data-ttu-id="fbf2b-176">Sertifika Kaynakları</span><span class="sxs-lookup"><span data-stu-id="fbf2b-176">Certificate sources</span></span>

<span data-ttu-id="fbf2b-177">Sertifika düğümleri, sertifikaları bir dizi kaynaktan yüklemek için yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-177">Certificate nodes can be configured to load certificates from a number of sources:</span></span>

* <span data-ttu-id="fbf2b-178">`Path``Password` *. pfx* dosyalarını yüklemek için.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-178">`Path` and `Password` to load *.pfx* files.</span></span>
* <span data-ttu-id="fbf2b-179">`Path``KeyPath`ve `Password` *. pek* / *. CRT* ve *. Key* dosyalarını yüklemek için.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-179">`Path`, `KeyPath` and `Password` to load *.pem*/*.crt* and *.key* files.</span></span>
* <span data-ttu-id="fbf2b-180">`Subject` ve `Store` sertifika deposundan yüklemek için.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-180">`Subject` and `Store` to load from the certificate store.</span></span>

<span data-ttu-id="fbf2b-181">Örneğin, sertifika şu `Certificates:Default` şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-181">For example, the `Certificates:Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a><span data-ttu-id="fbf2b-182">ConfigurationLoader</span><span class="sxs-lookup"><span data-stu-id="fbf2b-182">ConfigurationLoader</span></span>

<span data-ttu-id="fbf2b-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="fbf2b-184">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-184">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="fbf2b-185">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-185">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="fbf2b-186">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-186">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="fbf2b-187">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-187">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="fbf2b-188">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-188">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="fbf2b-189">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-189">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="fbf2b-190">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-190">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="fbf2b-191">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="fbf2b-191">Change the defaults in code</span></span>

<span data-ttu-id="fbf2b-192">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-192">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="fbf2b-193">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-193">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="fbf2b-194">Sunucu Adı Belirtme kullanarak uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="fbf2b-194">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="fbf2b-195">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-195">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="fbf2b-196">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-196">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="fbf2b-197">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-197">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="fbf2b-198">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-198">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="fbf2b-199">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-199">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="fbf2b-200">Aşağıdaki geri çağırma kodu `ConfigureWebHostDefaults` bir projenin *program.cs* dosyasının Yöntem çağrısında kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-200">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

<span data-ttu-id="fbf2b-201">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-201">SNI support requires:</span></span>

* <span data-ttu-id="fbf2b-202">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-202">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="fbf2b-203">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-203">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="fbf2b-204">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-204">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="fbf2b-205">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-205">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="fbf2b-206">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-206">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="ssltls-protocols"></a><span data-ttu-id="fbf2b-207">SSL/TLS protokolleri</span><span class="sxs-lookup"><span data-stu-id="fbf2b-207">SSL/TLS Protocols</span></span>

<span data-ttu-id="fbf2b-208">SSL protokolleri iki eş arasındaki trafiği şifrelemek ve şifresini çözmek için kullanılan protokollerdir, geleneksel olarak bir istemci ve sunucu.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-208">SSL Protocols are protocols used for encrypting and decrypting traffic between two peers, traditionally a client and a server.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

<span data-ttu-id="fbf2b-209">Varsayılan değer, `SslProtocols.None` Kestrel 'un en iyi Protokolü seçmesini sağlamak için işletim sistemi varsayılanlarını kullanmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-209">The default value, `SslProtocols.None`, causes Kestrel to use the operating system defaults to choose the best protocol.</span></span> <span data-ttu-id="fbf2b-210">Bir protokol seçmek için özel bir nedeniniz yoksa, Varsayılanı kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-210">Unless you have a specific reason to select a protocol, use the default.</span></span>
## <a name="connection-logging"></a><span data-ttu-id="fbf2b-211">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="fbf2b-211">Connection logging</span></span>

<span data-ttu-id="fbf2b-212"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-212">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="fbf2b-213">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-213">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="fbf2b-214">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-214">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="fbf2b-215">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-215">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="fbf2b-216">Bu yerleşik [bağlantı ara yazılımı](#connection-middleware).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-216">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="fbf2b-217">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="fbf2b-217">Bind to a TCP socket</span></span>

<span data-ttu-id="fbf2b-218"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="fbf2b-219">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-219">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="fbf2b-220">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-220">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="fbf2b-221">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="fbf2b-221">Bind to a Unix socket</span></span>

<span data-ttu-id="fbf2b-222"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-222">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="fbf2b-223">NGINX yapılandırma dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-223">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="fbf2b-224">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-224">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="fbf2b-225">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-225">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="fbf2b-226">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="fbf2b-226">Port 0</span></span>

<span data-ttu-id="fbf2b-227">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-227">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="fbf2b-228">Aşağıdaki örnek, çalışma zamanında Kestrel hangi bağlantı noktasının bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-228">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="fbf2b-229">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-229">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="fbf2b-230">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="fbf2b-230">Limitations</span></span>

<span data-ttu-id="fbf2b-231">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-231">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="fbf2b-232">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="fbf2b-232">`--urls` command-line argument</span></span>
* <span data-ttu-id="fbf2b-233">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="fbf2b-233">`urls` host configuration key</span></span>
* <span data-ttu-id="fbf2b-234">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="fbf2b-234">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="fbf2b-235">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-235">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="fbf2b-236">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-236">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="fbf2b-237">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmamışsa (örneğin, `KestrelServerOptions` Bu makalede daha önce gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılarak), https bu yaklaşımlar ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-237">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="fbf2b-238">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-238">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="fbf2b-239">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="fbf2b-239">IIS endpoint configuration</span></span>

<span data-ttu-id="fbf2b-240">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-240">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="fbf2b-241">Daha fazla bilgi için bkz. [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="fbf2b-241">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="fbf2b-242">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="fbf2b-242">ListenOptions.Protocols</span></span>

<span data-ttu-id="fbf2b-243">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-243">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="fbf2b-244">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-244">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="fbf2b-245">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="fbf2b-245">`HttpProtocols` enum value</span></span> | <span data-ttu-id="fbf2b-246">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="fbf2b-246">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="fbf2b-247">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-247">HTTP/1.1 only.</span></span> <span data-ttu-id="fbf2b-248">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-248">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="fbf2b-249">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-249">HTTP/2 only.</span></span> <span data-ttu-id="fbf2b-250">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-250">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="fbf2b-251">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-251">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="fbf2b-252">HTTP/2, istemcinin TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında http/2 seçmesini gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-252">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="fbf2b-253">`ListenOptions.Protocols`Herhangi bir uç nokta için varsayılan değer `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-253">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="fbf2b-254">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-254">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="fbf2b-255">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="fbf2b-255">TLS version 1.2 or later</span></span>
* <span data-ttu-id="fbf2b-256">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="fbf2b-256">Renegotiation disabled</span></span>
* <span data-ttu-id="fbf2b-257">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="fbf2b-257">Compression disabled</span></span>
* <span data-ttu-id="fbf2b-258">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-258">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="fbf2b-259">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="fbf2b-259">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="fbf2b-260">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="fbf2b-260">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="fbf2b-261">Şifre paketi yasaklandı.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-261">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="fbf2b-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="fbf2b-263">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-263">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="fbf2b-264">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-264">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="fbf2b-265">Linux 'ta, <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmaları her bağlantı temelinde filtrelemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-265">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a><span data-ttu-id="fbf2b-266">Bağlantı ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="fbf2b-266">Connection Middleware</span></span>

<span data-ttu-id="fbf2b-267">Özel bağlantı ara yazılımı, gerekirse belirli şifrelemeler için bağlantı başına TLS el sıkışmaları filtreleyebilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-267">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="fbf2b-268">Aşağıdaki örnek, <xref:System.NotSupportedException> uygulamanın desteklemediği herhangi bir şifre algoritması için oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-268">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="fbf2b-269">Alternatif olarak, [ılshandshakefeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) öğesini kabul edilebilir şifreleme paketleri listesi ile tanımlayın ve karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-269">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="fbf2b-270">[CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) şifre algoritması ile hiçbir şifreleme kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-270">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="fbf2b-271">Bağlantı filtrelemesi, bir lambda aracılığıyla da yapılandırılabilir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> :</span><span class="sxs-lookup"><span data-stu-id="fbf2b-271">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="fbf2b-272">HTTP protokolünü yapılandırmadan ayarla</span><span class="sxs-lookup"><span data-stu-id="fbf2b-272">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="fbf2b-273">`CreateDefaultBuilder``serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-273">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="fbf2b-274">Aşağıdaki *appsettings.json* örnek, tüm uç noktalar için varsayılan bağlantı protokolü olarak http/1.1 'yi kurar:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-274">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="fbf2b-275">Aşağıdaki *appsettings.json* örnek, belirli bir uç nokta IÇIN http/1.1 bağlantı protokolünü belirler:</span><span class="sxs-lookup"><span data-stu-id="fbf2b-275">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="fbf2b-276">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-276">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="fbf2b-277">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="fbf2b-277">URL prefixes</span></span>

<span data-ttu-id="fbf2b-278">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-278">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="fbf2b-279">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-279">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="fbf2b-280">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-280">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="fbf2b-281">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="fbf2b-281">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="fbf2b-282">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-282">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="fbf2b-283">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="fbf2b-283">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="fbf2b-284">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="fbf2b-284">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="fbf2b-285">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="fbf2b-285">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="fbf2b-286">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-286">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="fbf2b-287">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-287">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="fbf2b-288">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için [HTTP.sys](xref:fundamentals/servers/httpsys) veya bir ters proxy sunucusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-288">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="fbf2b-289">Ters proxy sunucu örnekleri IIS, NGINX veya Apache içerir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-289">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="fbf2b-290">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](xref:fundamentals/servers/kestrel/host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-290">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="fbf2b-291">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="fbf2b-291">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="fbf2b-292">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-292">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="fbf2b-293">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-293">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="fbf2b-294">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="fbf2b-294">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>
