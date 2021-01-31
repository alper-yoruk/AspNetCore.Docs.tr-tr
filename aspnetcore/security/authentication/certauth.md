---
title: ASP.NET Core sertifika kimlik doğrulamasını yapılandırma
author: blowdart
description: IIS ve HTTP.sys için ASP.NET Core sertifika kimlik doğrulamasını nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: c862bc8bff6c4cc80696d92067e814889d6e7782
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217537"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="4e76c-103">ASP.NET Core sertifika kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4e76c-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="4e76c-104">`Microsoft.AspNetCore.Authentication.Certificate` ASP.NET Core için [sertifika kimlik doğrulamasına](https://tools.ietf.org/html/rfc5246#section-7.4.4) benzer bir uygulama içerir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="4e76c-105">Sertifika kimlik doğrulaması TLS düzeyinde gerçekleşir ve bu süre ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4e76c-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="4e76c-106">Daha doğru, bu, sertifikayı doğrulayan bir kimlik doğrulama işleyicisidir ve bu sertifikayı bir ' a çözebileceğiniz bir olay verir `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="4e76c-107">[Sunucunuzu](#configure-your-server-to-require-certificates) sertifika kimlik doğrulaması için yapılandırın, BT IIS, Kestrel, Azure Web Apps veya kullandığınız başka herhangi bir şeydir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="4e76c-108">Proxy ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="4e76c-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="4e76c-109">Sertifika kimlik doğrulaması, genellikle bir ara sunucu veya yük dengeleyicinin istemciler ve sunucular arasındaki trafiği işleyememesi durumunda kullanılan, durum bilgisi olan bir senaryodur</span><span class="sxs-lookup"><span data-stu-id="4e76c-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="4e76c-110">Bir ara sunucu veya yük dengeleyici kullanılıyorsa, sertifika kimlik doğrulaması yalnızca proxy veya yük dengeleyici için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="4e76c-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="4e76c-111">Kimlik doğrulamasını işler.</span><span class="sxs-lookup"><span data-stu-id="4e76c-111">Handles the authentication.</span></span>
* <span data-ttu-id="4e76c-112">Kullanıcı kimlik doğrulama bilgilerini uygulamaya geçirir (örneğin, bir istek üstbilgisinde), kimlik doğrulama bilgileri üzerinde davranır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="4e76c-113">Proxy 'lerin ve yük dengeleyicilerin kullanıldığı ortamlarda sertifika kimlik doğrulamasına alternatif olarak, OpenID Connect (OıDC) ile Federasyon Hizmetleri (ADFS) Active Directory.</span><span class="sxs-lookup"><span data-stu-id="4e76c-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="4e76c-114">başlarken</span><span class="sxs-lookup"><span data-stu-id="4e76c-114">Get started</span></span>

<span data-ttu-id="4e76c-115">HTTPS sertifikası alın, uygulayın ve sunucunuzu sertifika gerektirecek şekilde [yapılandırın](#configure-your-server-to-require-certificates) .</span><span class="sxs-lookup"><span data-stu-id="4e76c-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="4e76c-116">Web uygulamanızda, [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4e76c-116">In your web app, add a reference to the [Microsoft.AspNetCore.Authentication.Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) package.</span></span> <span data-ttu-id="4e76c-117">Daha sonra `Startup.ConfigureServices` yönteminde, isteklerle birlikte `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` `OnCertificateValidated` gönderilen istemci sertifikasında herhangi bir destek doğrulaması yapmak için bir temsilci sağlayan seçeneklerinizde çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="4e76c-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="4e76c-118">Bu bilgileri bir öğesine dönüştürün `ClaimsPrincipal` ve özelliği üzerinde ayarlayın `context.Principal` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="4e76c-119">Kimlik doğrulaması başarısız olursa, bu işleyici `403 (Forbidden)` `401 (Unauthorized)` , bekleolabileceğiniz gibi bir yanıt döndürür.</span><span class="sxs-lookup"><span data-stu-id="4e76c-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="4e76c-120">Bu durum, kimlik doğrulamanın ilk TLS bağlantısı sırasında gerçekleşme nedendir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="4e76c-121">İşleyiciye ulaştığında, çok geç olur.</span><span class="sxs-lookup"><span data-stu-id="4e76c-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="4e76c-122">Anonim bir bağlantıyla bir sertifikayla bir bağlantıyı yükseltmenin bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="4e76c-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="4e76c-123">Yöntemine de ekleyin `app.UseAuthentication();` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="4e76c-124">Aksi takdirde, `HttpContext.User` sertifika, sertifikadan oluşturulacak şekilde ayarlanmayacak `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="4e76c-125">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4e76c-125">For example:</span></span>

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

<span data-ttu-id="4e76c-126">Önceki örnekte sertifika kimlik doğrulaması eklemenin varsayılan yolu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="4e76c-127">İşleyici, ortak sertifika özelliklerini kullanarak bir Kullanıcı sorumlusu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4e76c-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="4e76c-128">Sertifika doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4e76c-128">Configure certificate validation</span></span>

<span data-ttu-id="4e76c-129">`CertificateAuthenticationOptions`İşleyicide, bir sertifikada gerçekleştirmeniz gereken en düşük doğrulamalar olan yerleşik doğrulamalar vardır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="4e76c-130">Bu ayarların her biri varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="4e76c-131">AllowedCertificateTypes = zincirleme, SelfSigned veya All (zincirleme | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="4e76c-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="4e76c-132">Varsayılan değer: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="4e76c-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="4e76c-133">Bu denetim yalnızca uygun sertifika türüne izin verildiğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="4e76c-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="4e76c-134">Uygulama otomatik olarak imzalanan sertifikalar kullanıyorsa, bu seçeneğin veya olarak ayarlanması gerekir `CertificateTypes.All` `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="4e76c-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="4e76c-135">ValidateCertificateUse</span></span>

<span data-ttu-id="4e76c-136">Varsayılan değer: `true`</span><span class="sxs-lookup"><span data-stu-id="4e76c-136">Default value: `true`</span></span>

<span data-ttu-id="4e76c-137">Bu denetim, istemci tarafından sunulan sertifikanın Istemci kimlik doğrulaması genişletilmiş anahtar kullanımı (EKU) olduğunu veya hiç EKU olmadığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="4e76c-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="4e76c-138">Belirtimlerde bir EKU belirtilmemişse, tüm EKU 'lar geçerli kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="4e76c-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="4e76c-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="4e76c-140">Varsayılan değer: `true`</span><span class="sxs-lookup"><span data-stu-id="4e76c-140">Default value: `true`</span></span>

<span data-ttu-id="4e76c-141">Bu denetim, sertifikanın geçerlilik süresi içinde olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="4e76c-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="4e76c-142">Her istekte işleyici, geçerli oturumu sırasında sunulmadığı zaman geçerli olmayan bir sertifikanın süresinin dolmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="4e76c-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="4e76c-143">Revocationbayrağı</span><span class="sxs-lookup"><span data-stu-id="4e76c-143">RevocationFlag</span></span>

<span data-ttu-id="4e76c-144">Varsayılan değer: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="4e76c-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="4e76c-145">Zincirdeki hangi sertifikaların iptal için denetleneceğini belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="4e76c-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="4e76c-146">İptal denetimleri yalnızca sertifika bir kök sertifikaya zincirleme yapıldığında gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="4e76c-147">Revocationmodu</span><span class="sxs-lookup"><span data-stu-id="4e76c-147">RevocationMode</span></span>

<span data-ttu-id="4e76c-148">Varsayılan değer: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="4e76c-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="4e76c-149">İptal denetimlerinin nasıl gerçekleştirileceğini belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="4e76c-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="4e76c-150">Bir çevrimiçi denetim belirtildiğinde, sertifika yetkilisi ile bağlantı kurulduğunda uzun bir gecikmeyle sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="4e76c-151">İptal denetimleri yalnızca sertifika bir kök sertifikaya zincirleme yapıldığında gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="4e76c-152">Uygulamamı yalnızca belirli yollarda sertifika gerektirecek şekilde yapılandırabilir miyim?</span><span class="sxs-lookup"><span data-stu-id="4e76c-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="4e76c-153">Bu mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-153">This isn't possible.</span></span> <span data-ttu-id="4e76c-154">Sertifika değişimi 'nin HTTPS konuşması başlangıcı olduğunu unutmayın, bu bağlantı üzerinde ilk istek alınmadan önce sunucu tarafından gerçekleştirilir, böylece herhangi bir istek alanı temelinde kapsam yapılamaz.</span><span class="sxs-lookup"><span data-stu-id="4e76c-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="4e76c-155">İşleyici olayları</span><span class="sxs-lookup"><span data-stu-id="4e76c-155">Handler events</span></span>

<span data-ttu-id="4e76c-156">İşleyicinin iki olayı vardır:</span><span class="sxs-lookup"><span data-stu-id="4e76c-156">The handler has two events:</span></span>

* <span data-ttu-id="4e76c-157">`OnAuthenticationFailed`: Kimlik doğrulaması sırasında bir özel durum oluşursa ve tepki vermenize izin veriyorsa çağırılır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="4e76c-158">`OnCertificateValidated`: Sertifika doğrulandıktan sonra, doğrulama geçildi ve bir varsayılan asıl oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4e76c-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="4e76c-159">Bu olay kendi doğrulamayı gerçekleştirmenize ve sorumluyu artırabilir veya değiştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="4e76c-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="4e76c-160">Örnekler için şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="4e76c-160">For examples include:</span></span>
  * <span data-ttu-id="4e76c-161">Sertifikanın hizmetlerinize göre bilinip tanınmadığını belirleme.</span><span class="sxs-lookup"><span data-stu-id="4e76c-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="4e76c-162">Kendi sorumlunuzu oluşturma.</span><span class="sxs-lookup"><span data-stu-id="4e76c-162">Constructing your own principal.</span></span> <span data-ttu-id="4e76c-163">İçinde aşağıdaki örneği göz önünde bulundurun `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4e76c-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4e76c-164">Gelen sertifikayı, ek doğrulamadan uymadığını fark ederseniz `context.Fail("failure reason")` bir hata nedeniyle çağırın.</span><span class="sxs-lookup"><span data-stu-id="4e76c-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="4e76c-165">Gerçek işlevsellik için muhtemelen bir veritabanına veya diğer Kullanıcı Mağazası türüne bağlanan bağımlılık ekleme bölümünde kayıtlı bir hizmeti çağırmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="4e76c-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="4e76c-166">Temsilciniz 'e geçirilen bağlamı kullanarak hizmetinize erişin.</span><span class="sxs-lookup"><span data-stu-id="4e76c-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="4e76c-167">İçinde aşağıdaki örneği göz önünde bulundurun `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4e76c-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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
                        .GetRequiredService<ICertificateValidationService>();
                
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

<span data-ttu-id="4e76c-168">Kavramsal olarak, sertifikanın doğrulanması bir yetkilendirme konusudur.</span><span class="sxs-lookup"><span data-stu-id="4e76c-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="4e76c-169">Örneğin, bir yetkilendirme ilkesindeki bir veren veya parmak izi gibi bir denetim eklemek, `OnCertificateValidated` mükemmel bir kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="4e76c-170">Sunucunuzu sertifika gerektirecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4e76c-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="4e76c-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="4e76c-171">Kestrel</span></span>

<span data-ttu-id="4e76c-172">*Program.cs*' de, Kestrel ' yi aşağıdaki şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="4e76c-172">In *Program.cs*, configure Kestrel as follows:</span></span>

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
> <span data-ttu-id="4e76c-173">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="4e76c-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="4e76c-174">IIS</span><span class="sxs-lookup"><span data-stu-id="4e76c-174">IIS</span></span>

<span data-ttu-id="4e76c-175">IIS Yöneticisi 'nde aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="4e76c-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="4e76c-176">**Bağlantılar** sekmesinden sitenizi seçin.</span><span class="sxs-lookup"><span data-stu-id="4e76c-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="4e76c-177">**Özellikler Görünümü** penceresinde **SSL ayarları** seçeneğine çift tıklayın.</span><span class="sxs-lookup"><span data-stu-id="4e76c-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="4e76c-178">**SSL gerektir** onay kutusunu Işaretleyin ve **istemci sertifikaları** bölümünde radyo **iste** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="4e76c-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS 'de istemci sertifikası ayarları](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="4e76c-180">Azure ve özel Web proxy 'leri</span><span class="sxs-lookup"><span data-stu-id="4e76c-180">Azure and custom web proxies</span></span>

<span data-ttu-id="4e76c-181">Sertifika iletme ara yazılımını yapılandırma hakkında bilgi için bkz. [konak ve dağıtım belgeleri](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .</span><span class="sxs-lookup"><span data-stu-id="4e76c-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="4e76c-182">Azure Web Apps sertifika kimlik doğrulamasını kullanma</span><span class="sxs-lookup"><span data-stu-id="4e76c-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="4e76c-183">Azure için bir iletme yapılandırması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="4e76c-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="4e76c-184">Bu, sertifika iletme ara ortamında zaten kuruldu.</span><span class="sxs-lookup"><span data-stu-id="4e76c-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="4e76c-185">Bu, Certificateforwardingara yazılımı 'nın mevcut olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="4e76c-186">Özel Web proxy 'lerinde sertifika kimlik doğrulamasını kullanma</span><span class="sxs-lookup"><span data-stu-id="4e76c-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="4e76c-187">`AddCertificateForwarding`Yöntemi belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4e76c-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="4e76c-188">İstemci üst bilgi adı.</span><span class="sxs-lookup"><span data-stu-id="4e76c-188">The client header name.</span></span>
* <span data-ttu-id="4e76c-189">Sertifika nasıl yüklenir ( `HeaderConverter` özelliğini kullanarak).</span><span class="sxs-lookup"><span data-stu-id="4e76c-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="4e76c-190">Özel Web proxy 'lerinde, sertifika özel bir istek üst bilgisi olarak geçirilir, örneğin `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="4e76c-191">Bunu kullanmak için, içinde sertifika iletmeyi yapılandırın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4e76c-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4e76c-192">`Startup.Configure`Yöntemi daha sonra ara yazılımı ekler.</span><span class="sxs-lookup"><span data-stu-id="4e76c-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="4e76c-193">`UseCertificateForwarding` , ve çağrıları için çağrılır `UseAuthentication` `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="4e76c-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

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

<span data-ttu-id="4e76c-194">Ayrı bir sınıf, doğrulama mantığını uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="4e76c-195">Bu örnekte otomatik olarak imzalanan sertifika kullanıldığından, yalnızca sertifikanızın kullanılabildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="4e76c-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="4e76c-196">İstemci sertifikasının ve sunucu sertifikasının parmak izlerinin eşleştiğinden emin olun, aksi takdirde herhangi bir sertifika kullanılabilir ve kimlik doğrulaması için yeterli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="4e76c-197">Bu, yöntemi içinde kullanılacaktır `AddCertificate` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="4e76c-198">Ara veya alt sertifikalar kullanıyorsanız konuyu veya sertifikayı vereni de doğrulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4e76c-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="4e76c-199">Sertifika ve HttpClientHandler kullanarak bir HttpClient uygulama</span><span class="sxs-lookup"><span data-stu-id="4e76c-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="4e76c-200">, `HttpClientHandler` Sınıfının oluşturucusuna doğrudan eklenebilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-200">The `HttpClientHandler` could be added directly in the constructor of the `HttpClient` class.</span></span> <span data-ttu-id="4e76c-201">Örnekleri oluşturulurken dikkatli olunmalıdır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-201">Care should be taken when creating instances of the `HttpClient`.</span></span> <span data-ttu-id="4e76c-202">`HttpClient`Ardından, her istek ile sertifikayı gönderir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-202">The `HttpClient` will then send the certificate with each request.</span></span>

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

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="4e76c-203">Bir sertifika ve ıhttpclientfactory adlı bir HttpClient kullanarak bir HttpClient uygulama</span><span class="sxs-lookup"><span data-stu-id="4e76c-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="4e76c-204">Aşağıdaki örnekte, bir istemci sertifikası, `HttpClientHandler` işleyicisinden özelliği kullanılarak bir öğesine eklenir `ClientCertificates` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-204">In the following example, a client certificate is added to a `HttpClientHandler` using the `ClientCertificates` property from the handler.</span></span> <span data-ttu-id="4e76c-205">Bu işleyici daha sonra yöntemi kullanılarak bir adlandırılmış örneğinde kullanılabilir `HttpClient` `ConfigurePrimaryHttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-205">This handler can then be used in a named instance of an `HttpClient` using the `ConfigurePrimaryHttpMessageHandler` method.</span></span> <span data-ttu-id="4e76c-206">Bu kurulum `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4e76c-206">This is setup in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4e76c-207">`IHttpClientFactory`Daha sonra, adlandırılmış örneği işleyicinin ve sertifikayla almak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-207">The `IHttpClientFactory` can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="4e76c-208">`CreateClient`Sınıfında tanımlanan istemci adına sahip Yöntem `Startup` örneği almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-208">The `CreateClient` method with the name of the client defined in the `Startup` class is used to get the instance.</span></span> <span data-ttu-id="4e76c-209">HTTP isteği, gereken şekilde istemci kullanılarak gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-209">The HTTP request can be sent using the client as required.</span></span>

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

<span data-ttu-id="4e76c-210">Sunucuya doğru sertifika gönderiliyorsa, veriler döndürülür.</span><span class="sxs-lookup"><span data-stu-id="4e76c-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="4e76c-211">Sertifika yoksa veya yanlış sertifika gönderilmezse bir HTTP 403 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="4e76c-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="4e76c-212">PowerShell 'de sertifika oluşturma</span><span class="sxs-lookup"><span data-stu-id="4e76c-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="4e76c-213">Sertifikaların oluşturulması, bu akışı ayarlamanın en zor bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="4e76c-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="4e76c-214">Bir kök sertifika, `New-SelfSignedCertificate` PowerShell cmdlet 'i kullanılarak oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="4e76c-215">Sertifikayı oluştururken güçlü bir parola kullanın.</span><span class="sxs-lookup"><span data-stu-id="4e76c-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="4e76c-216">`KeyUsageProperty`Parametresi ve `KeyUsage` parametresini gösterildiği gibi eklemek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="4e76c-217">Kök CA oluştur</span><span class="sxs-lookup"><span data-stu-id="4e76c-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="4e76c-218">`-DnsName`Parametre değeri uygulamanın dağıtım hedefi ile aynı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="4e76c-219">Örneğin, geliştirme için "localhost".</span><span class="sxs-lookup"><span data-stu-id="4e76c-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="4e76c-220">Güvenilen köke yüklensin</span><span class="sxs-lookup"><span data-stu-id="4e76c-220">Install in the trusted root</span></span>

<span data-ttu-id="4e76c-221">Kök sertifikanın ana bilgisayar sisteminizde güvenilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="4e76c-222">Sertifika yetkilisi tarafından oluşturulmamış bir kök sertifikaya varsayılan olarak güvenilmez.</span><span class="sxs-lookup"><span data-stu-id="4e76c-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="4e76c-223">Aşağıdaki bağlantıda bunun Windows 'da nasıl gerçekleştirebileceği açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="4e76c-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="4e76c-224">Ara sertifika</span><span class="sxs-lookup"><span data-stu-id="4e76c-224">Intermediate certificate</span></span>

<span data-ttu-id="4e76c-225">Artık kök sertifikadan bir ara sertifika oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="4e76c-226">Bu, tüm kullanım durumları için gerekli değildir, ancak birçok sertifika oluşturmanız veya sertifika gruplarını etkinleştirmeniz veya devre dışı bırakmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="4e76c-227">`TextExtension`Sertifikanın temel kısıtlamalarında yol uzunluğunu ayarlamak için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="4e76c-228">Ara sertifika daha sonra Windows ana bilgisayar sistemindeki güvenilen ara sertifikaya eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="4e76c-229">Ara sertifikadan alt sertifika oluştur</span><span class="sxs-lookup"><span data-stu-id="4e76c-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="4e76c-230">Ara sertifikadan bir alt sertifika oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="4e76c-231">Bu, son varlıktır ve daha fazla alt sertifika oluşturmak zorunda değildir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="4e76c-232">Kök sertifikadan alt sertifika oluştur</span><span class="sxs-lookup"><span data-stu-id="4e76c-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="4e76c-233">Kök sertifikadan doğrudan bir alt sertifika da oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="4e76c-234">Örnek kök-ara sertifika-sertifika</span><span class="sxs-lookup"><span data-stu-id="4e76c-234">Example root - intermediate certificate - certificate</span></span>

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

<span data-ttu-id="4e76c-235">Kök, ara veya alt sertifikaları kullanırken, sertifikalar, Parmak Izi veya PublicKey kullanılarak gerektiği şekilde doğrulanabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

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

## <a name="certificate-validation-caching"></a><span data-ttu-id="4e76c-236">Sertifika doğrulama önbelleği</span><span class="sxs-lookup"><span data-stu-id="4e76c-236">Certificate validation caching</span></span>

<span data-ttu-id="4e76c-237">ASP.NET Core 5,0 ve sonraki sürümler, doğrulama sonuçlarının önbelleğe alınmasını etkinleştirme yeteneğini destekler.</span><span class="sxs-lookup"><span data-stu-id="4e76c-237">ASP.NET Core 5.0 and later versions support the ability to enable caching of validation results.</span></span> <span data-ttu-id="4e76c-238">Doğrulama pahalı bir işlem olduğundan, önbelleğe alma, sertifika kimlik doğrulamasının performansını önemli ölçüde artırır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-238">The caching dramatically improves performance of certificate authentication, as validation is an expensive operation.</span></span>

<span data-ttu-id="4e76c-239">Sertifika kimlik doğrulaması varsayılan olarak önbelleğe almayı devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-239">By default, certificate authentication disables caching.</span></span> <span data-ttu-id="4e76c-240">Önbelleğe almayı etkinleştirmek için şunu `AddCertificateCache` arayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4e76c-240">To enable caching, call `AddCertificateCache` in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4e76c-241">Varsayılan önbelleğe alma uygulama sonuçları bellekte depolar.</span><span class="sxs-lookup"><span data-stu-id="4e76c-241">The default caching implementation stores results in memory.</span></span> <span data-ttu-id="4e76c-242">Bağımlılık ekleme ile kaydederek kendi önbelleğinizi sağlayabilirsiniz `ICertificateValidationCache` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-242">You can provide your own cache by implementing `ICertificateValidationCache` and registering it with dependency injection.</span></span> <span data-ttu-id="4e76c-243">Örneğin, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span><span class="sxs-lookup"><span data-stu-id="4e76c-243">For example, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span></span>

::: moniker-end

## <a name="optional-client-certificates"></a><span data-ttu-id="4e76c-244">İsteğe bağlı istemci sertifikaları</span><span class="sxs-lookup"><span data-stu-id="4e76c-244">Optional client certificates</span></span>

<span data-ttu-id="4e76c-245">Bu bölüm, bir sertifika ile uygulamanın bir alt kümesini koruması gereken uygulamalar için bilgiler sağlar.</span><span class="sxs-lookup"><span data-stu-id="4e76c-245">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="4e76c-246">Örneğin, uygulamadaki bir Razor sayfa veya denetleyici istemci sertifikaları gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-246">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="4e76c-247">Bu, zorlukları istemci sertifikaları olarak gösterir:</span><span class="sxs-lookup"><span data-stu-id="4e76c-247">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="4e76c-248">, HTTP özelliği değil, bir TLS özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-248">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="4e76c-249">, Bağlantı başına anlaşma yapılır ve herhangi bir HTTP verisi kullanılabilir olmadan önce bağlantının başlangıcında anlaşılıp verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-249">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="4e76c-250">Bağlantının başlangıcında yalnızca Sunucu Adı Belirtme (SNı) &dagger; bilinirdi.</span><span class="sxs-lookup"><span data-stu-id="4e76c-250">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="4e76c-251">İstemci ve sunucu sertifikaları, bir bağlantı üzerindeki ilk istekten önce görüşülür ve istekler genellikle yeniden anlaşma yapamaz.</span><span class="sxs-lookup"><span data-stu-id="4e76c-251">The client and server certificates are negotiated prior to the first request on a connection and requests generally aren't able to renegotiate.</span></span>

<span data-ttu-id="4e76c-252">TLS yeniden anlaşması, isteğe bağlı istemci sertifikaları uygulamak için eski bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="4e76c-252">TLS renegotiation was an old way to implement optional client certificates.</span></span> <span data-ttu-id="4e76c-253">Bu artık önerilmez, çünkü:</span><span class="sxs-lookup"><span data-stu-id="4e76c-253">This is no longer recommended because:</span></span>
- <span data-ttu-id="4e76c-254">HTTP/1.1 ' de, bir POST isteği sırasında yeniden anlaşma, istek gövdesinin TCP penceresini doldurduğu ve yeniden anlaşma paketlerinin alınmamasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-254">In HTTP/1.1, renegotiating during a POST request could cause a deadlock where the request body filled up the TCP window and the renegotiation packets can't be received.</span></span>
- <span data-ttu-id="4e76c-255">HTTP/2 [açıkça](https://tools.ietf.org/html/rfc7540#section-9.2.1) yeniden anlaşmaya izin vermez.</span><span class="sxs-lookup"><span data-stu-id="4e76c-255">HTTP/2 [explicitly prohibits](https://tools.ietf.org/html/rfc7540#section-9.2.1) renegotiation.</span></span>
- <span data-ttu-id="4e76c-256">TLS 1,3, yeniden anlaşma desteğini [kaldırdı](https://tools.ietf.org/html/rfc8740#section-1) .</span><span class="sxs-lookup"><span data-stu-id="4e76c-256">TLS 1.3 has [removed](https://tools.ietf.org/html/rfc8740#section-1) support for renegotiation.</span></span>

<span data-ttu-id="4e76c-257">ASP.NET Core 5 Preview 7 ve üzeri, isteğe bağlı istemci sertifikaları için daha kolay destek ekler.</span><span class="sxs-lookup"><span data-stu-id="4e76c-257">ASP.NET Core 5 preview 7 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="4e76c-258">Daha fazla bilgi için bkz. [Isteğe bağlı sertifikalar örneği](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span><span class="sxs-lookup"><span data-stu-id="4e76c-258">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="4e76c-259">Aşağıdaki yaklaşım isteğe bağlı istemci sertifikalarını destekler:</span><span class="sxs-lookup"><span data-stu-id="4e76c-259">The following approach supports optional client certificates:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="4e76c-260">Etki alanı ve alt etki alanı için bağlamayı ayarlama:</span><span class="sxs-lookup"><span data-stu-id="4e76c-260">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="4e76c-261">Örneğin, ve üzerinde bağlamaları ayarlayın `contoso.com` `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-261">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="4e76c-262">`contoso.com`Konakta istemci sertifikası gerekmez `myClient.contoso.com` , ancak bunu yapar.</span><span class="sxs-lookup"><span data-stu-id="4e76c-262">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="4e76c-263">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4e76c-263">For more information, see:</span></span>
    * <span data-ttu-id="4e76c-264">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="4e76c-264">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="4e76c-265">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="4e76c-265">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel/endpoints#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="4e76c-266">Not Kestrel Şu anda tek bir bağlamada birden fazla TLS yapılandırmasını desteklemez, benzersiz IP 'Ler veya bağlantı noktalarıyla iki bağlama gerekecektir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-266">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="4e76c-267">Bkz. https://github.com/dotnet/runtime/issues/31097</span><span class="sxs-lookup"><span data-stu-id="4e76c-267">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="4e76c-268">IIS</span><span class="sxs-lookup"><span data-stu-id="4e76c-268">IIS</span></span>
      * [<span data-ttu-id="4e76c-269">IIS barındırma</span><span class="sxs-lookup"><span data-stu-id="4e76c-269">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="4e76c-270">IIS 'de güvenliği yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4e76c-270">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="4e76c-271">HTTP.sys: [Windows Server 'ı yapılandırma](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="4e76c-271">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="4e76c-272">Etki alanı ve alt etki alanı için bağlamayı ayarlama:</span><span class="sxs-lookup"><span data-stu-id="4e76c-272">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="4e76c-273">Örneğin, ve üzerinde bağlamaları ayarlayın `contoso.com` `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-273">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="4e76c-274">`contoso.com`Konakta istemci sertifikası gerekmez `myClient.contoso.com` , ancak bunu yapar.</span><span class="sxs-lookup"><span data-stu-id="4e76c-274">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="4e76c-275">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4e76c-275">For more information, see:</span></span>
    * <span data-ttu-id="4e76c-276">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="4e76c-276">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="4e76c-277">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="4e76c-277">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="4e76c-278">Not Kestrel Şu anda tek bir bağlamada birden fazla TLS yapılandırmasını desteklemez, benzersiz IP 'Ler veya bağlantı noktalarıyla iki bağlama gerekecektir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-278">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="4e76c-279">Bkz. https://github.com/dotnet/runtime/issues/31097</span><span class="sxs-lookup"><span data-stu-id="4e76c-279">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="4e76c-280">IIS</span><span class="sxs-lookup"><span data-stu-id="4e76c-280">IIS</span></span>
      * [<span data-ttu-id="4e76c-281">IIS barındırma</span><span class="sxs-lookup"><span data-stu-id="4e76c-281">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="4e76c-282">IIS 'de güvenliği yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4e76c-282">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="4e76c-283">HTTP.sys: [Windows Server 'ı yapılandırma](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="4e76c-283">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

* <span data-ttu-id="4e76c-284">İstemci sertifikası gerektiren ve bir tane olmayan Web uygulamasına yönelik istekler için:</span><span class="sxs-lookup"><span data-stu-id="4e76c-284">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="4e76c-285">İstemci sertifikası korumalı alt etki alanını kullanarak aynı sayfaya yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="4e76c-285">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="4e76c-286">Örneğin, öğesine yönlendirin `myClient.contoso.com/requestedPage` .</span><span class="sxs-lookup"><span data-stu-id="4e76c-286">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="4e76c-287">İsteği `myClient.contoso.com/requestedPage` öğesinden farklı bir ana bilgisayar adı olduğundan `contoso.com/requestedPage` , istemci farklı bir bağlantı oluşturur ve istemci sertifikası sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-287">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="4e76c-288">Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="4e76c-288">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="4e76c-289">[Bu GitHub tartışma](https://github.com/dotnet/AspNetCore.Docs/issues/18720) sorununa yönelik isteğe bağlı istemci sertifikalarıyla ilgili soruları, açıklamaları ve diğer geri bildirimleri bırakın.</span><span class="sxs-lookup"><span data-stu-id="4e76c-289">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="4e76c-290">&dagger; Sunucu Adı Belirtme (SNı), SSL anlaşmasının bir parçası olarak sanal etki alanı dahil etmek için bir TLS uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="4e76c-290">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="4e76c-291">Bu, sanal etki alanı adının veya ana bilgisayar adının ağ uç noktasını tanımlamak için kullanılabileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="4e76c-291">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>
