---
title: Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma
author: rick-anderson
description: Genellikle önemli istek bilgilerini gizleyen ara sunucu ve yük dengeleyiciler arkasında barındırılan uygulamalar için yapılandırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e329b8604b820818167a563b3a21f01f2ab2a6ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776389"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma

[Chris](https://github.com/Tratcher) 'e göre

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core için Önerilen yapılandırmada, uygulama IIS/ASP. NET Core modülü, NGINX veya Apache kullanılarak barındırılır. Proxy sunucuları, yük dengeleyiciler ve diğer ağ gereçleri, genellikle istek hakkında uygulamaya ulaşmadan önce bu bilgileri gizlemediğinde:

* HTTPS istekleri HTTP üzerinden proxy olduğunda, özgün düzen (HTTPS) kaybolur ve bir üst bilgide iletilmesi gerekir.
* Bir uygulama, Internet veya şirket ağında gerçek kaynağını değil, ara sunucudan bir istek aldığından, kaynak istemci IP adresinin de bir üst bilgide iletilmesi gerekir.

Bu bilgiler, istek işlemede, örneğin yeniden yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konum gibi önemli olabilir.

## <a name="forwarded-headers"></a>İletilen üstbilgiler

Kurala göre, proxy 'leri HTTP başlıklarında iletme bilgileri.

| Üst bilgi | Açıklama |
| ------ | ----------- |
| X-Iletilmiş-Için | , İsteği başlatan istemciyle ilgili bilgileri ve bir proxy zincirinde sonraki proxy 'leri barındırır. Bu parametre IP adresleri (ve isteğe bağlı olarak bağlantı noktası numaraları) içerebilir. Bir ara sunucu zincirinde, ilk parametre isteğin ilk yaptığı istemciyi gösterir. Sonraki proxy tanımlayıcıları izler. Zincirdeki son proxy, Parametreler listesinde değil. Son proxy 'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanında uzak IP adresi olarak kullanılabilir. |
| X-Iletilen-proto | Kaynak düzenin değeri (HTTP/HTTPS). Bu değer, istek birden çok proxy 'ye geçen bir düzen listesi de olabilir. |
| X-Iletilen-konak | Ana bilgisayar üst bilgisi alanının özgün değeri. Genellikle, proxy 'ler ana bilgisayar üst bilgisini değiştirmez. Proxy 'nin konak üstbilgilerini doğrulamadığı veya kısıtlayamayacağı sistemleri etkileyen ayrıcalık yükselmesi güvenlik açığı hakkında bilgi için bkz. [Microsoft Güvenlik Danışmanlığı CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) . |

[Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki Iletilen üstbilgiler ara yazılımı, bu üst bilgileri okur ve ilgili alanları üzerinde <xref:Microsoft.AspNetCore.Http.HttpContext>doldurur.

Ara yazılım güncelleştirmeleri:

* Üst bilgi değerini kullanarak [HttpContext. Connection. remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; kümesi. `X-Forwarded-For` Ek ayarlar, ara yazılımın nasıl ayarlanacağını `RemoteIpAddress`etkiler. Ayrıntılar için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options).
* Üst bilgi değerini kullanan [HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; kümesi. `X-Forwarded-Proto`
* Üst bilgi değerini kullanan [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; kümesi. `X-Forwarded-Host`

İletilen üstbilgiler ara yazılımı [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir. Varsayılan ayarlar şunlardır:

* Uygulama ve isteklerin kaynağı arasında yalnızca *bir ara sunucu* vardır.
* Bilinen proxy 'ler ve bilinen ağlar için yalnızca geri döngü adresleri yapılandırılır.
* İletilen üstbilgiler ve `X-Forwarded-Proto`olarak adlandırılır `X-Forwarded-For` .

Tüm ağ gereçleri ek yapılandırma olmadan `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini eklemez. Proxy istekler uygulamaya ulaştığında bu üst bilgileri içermiyorsa, Gereç üreticinizin kılavuzuna başvurun. Gereç `X-Forwarded-For` , ve `X-Forwarded-Proto`dışındaki farklı üstbilgi adlarını kullanıyorsa, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini gereç tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın. Daha fazla bilgi için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) ve [farklı üstbilgi adları kullanan bir proxy için yapılandırma](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS/IIS Express ve ASP.NET Core modülü

İletilen üstbilgiler ara yazılımı, uygulama IIS 'nin arkasında ve ASP.NET Core modülünden sonra [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) barındırılıyorsa [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından varsayılan olarak etkinleştirilir. İletilen üstbilgiler ara yazılımı, iletilen üst bilgilerle (örneğin, [IP aldatmacası](https://www.iplocation.net/ip-spoofing)) güven sorunları nedeniyle ASP.NET Core modülüne özgü kısıtlanmış bir yapılandırmaya sahip olan ara yazılım ardışık düzeninde ilk olarak çalışır. Ara yazılım, `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini iletmek üzere yapılandırılmıştır ve tek bir localhost proxy ile kısıtlanır. Ek yapılandırma gerekliyse, [Iletilen üstbilgiler ara yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Diğer proxy sunucu ve yük dengeleyici senaryoları

[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında [IIS tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 'nin kullanılması dışında, iletilen üstbilgiler ara yazılımı varsayılan olarak etkinleştirilmemiştir. İletilen üstbilgileri ile <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>işlemek için bir uygulamanın yönlendirilmiş üst bilgi ara yazılımı etkinleştirilmelidir. Ara yazılım için Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse, ara yazılımı etkinleştirdikten sonra varsayılan [Forwardedheadersoptions. Forwardedheaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) , [forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dir.

İle <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ara yazılımı, içindeki `X-Forwarded-For` `X-Forwarded-Proto` `Startup.ConfigureServices`ve üst bilgilerini iletecek şekilde yapılandırın. Diğer ara <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> yazılım çağrılmadan `Startup.Configure` önce metodunu çağırın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `Startup.ConfigureServices` veya doğrudan genişletme yöntemine belirtilmemişse <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, Iletmek için varsayılan üstbilgiler [forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Özelliğin, iletmek için üst bilgilerle yapılandırılması gerekir.

## <a name="nginx-configuration"></a>NGINX yapılandırması

`X-Forwarded-For` Ve `X-Forwarded-Proto` üst bilgilerini iletmek için bkz <xref:host-and-deploy/linux-nginx#configure-nginx>.. Daha fazla bilgi için bkz. [NGINX: iletilen üstbilgiyi kullanma](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Apache yapılandırması

`X-Forwarded-For`otomatik olarak eklenir (bkz. [Apache modülü mod_proxy: ters proxy Istek üstbilgileri](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). `X-Forwarded-Proto` Üstbilgiyi iletme hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-apache#configure-apache>..

## <a name="forwarded-headers-middleware-options"></a>İletilen üstbilgiler ara yazılım seçenekleri

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen üstbilgiler ara yazılımı davranışını denetleyin. Aşağıdaki örnek varsayılan değerleri değiştirir:

* İletilen üst bilgilerdeki giriş sayısını sınırlayın `2`.
* Bilinen bir proxy adresi ekleyin `127.0.10.1`.
* İletilen üst bilgi adını varsayılan `X-Forwarded-For` değerinden olarak `X-Forwarded-For-My-Custom-Header-Name`değiştirin.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Seçenek | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ana bilgisayarları `X-Forwarded-Host` üst bilgiyle belirtilen değerlerle sınırlandırır.<ul><li>Değerler, Ordinal-Ignore-Case kullanılarak karşılaştırılır.</li><li>Bağlantı noktası numaraları dışlanmalıdır.</li><li>Liste boşsa, tüm konaklara izin verilir.</li><li>Üst düzey joker karakter `*` boş olmayan tüm konaklara izin verir.</li><li>Alt etki alanı Joker karakterlere izin verilir ancak kök etki alanıyla eşleşmez. Örneğin, `*.contoso.com` kök etki alanını değil `foo.contoso.com` , alt etki alanıyla `contoso.com`eşleşir.</li><li>Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [puni koduna](https://tools.ietf.org/html/rfc3492) dönüştürülür.</li><li>[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı ayraçları içermeli ve [geleneksel biçimde](https://tools.ietf.org/html/rfc4291#section-2.2) olmalıdır (örneğin, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). IPv6 adresleri, farklı biçimler arasında mantıksal eşitlik denetimi için özel değildir ve hiçbir kurallı kullanım yapılmaz.</li><li>İzin verilen konaklar kısıtlanamaması, bir saldırganın hizmet tarafından oluşturulan bağlantıları sızdırmasına izin verebilir.</li></ul>Varsayılan değer boş `IList<string>`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen bir yerine kullanın. Bu seçenek, proxy/iletici `X-Forwarded-For` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Hangi ileticilerin işleneceğini tanımlar. Uygulanan alanların listesi için [Forwardedheaders numaralandırması](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) ' ne bakın. Bu özelliğe atanan tipik değerler şunlardır `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Varsayılan değer [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [Forwardedheadersdefaults varsayılan. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın. Bu seçenek, proxy/iletici `X-Forwarded-Host` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen bir yerine kullanın. Bu seçenek, proxy/iletici `X-Forwarded-Proto` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | İşlenen üst bilgilerdeki giriş sayısını sınırlandırır. Sınırı devre `null` dışı bırakmak için olarak ayarlayın, ancak bu yalnızca `KnownProxies` veya `KnownNetworks` yapılandırılmışsa yapılmalıdır. `null` Değer olmayan bir önlem, hatalı yapılandırılmış proxy 'lerde ve ağdaki yan kanallardan gelen kötü amaçlı isteklere karşı koruma sağlamak için bir önlem (garanti değil) olarak ayarlanır.<br><br>İletilen üstbilgiler ara yazılımı, üst bilgileri sağdan sola doğru sırada işler. Varsayılan değer (`1`) kullanılırsa, değeri `ForwardLimit` artmadığı müddetçe yalnızca üst bilgilerden en sağdaki değer işlenir.<br><br>Varsayılan değer: `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | İletilen üstbilgileri kabul etmek için bilinen ağların adres aralıkları. Sınıfsız Etki alanları arası yönlendirme (CıDR) gösterimini kullanarak IP aralıkları sağlayın.<br><br>Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak `::ffff:10.0.0.1`temsil edilen IPv4 içinde). Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın. Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.<br><br>Varsayılan `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> , için `IPAddress.Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | İletilen üstbilgileri kabul etmek için bilinen proxy 'lerin adresleri. Tam `KnownProxies` IP adresi eşleşmelerini belirtmek için kullanın.<br><br>Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak `::ffff:10.0.0.1`temsil edilen IPv4 içinde). Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın. Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.<br><br>Varsayılan `IList` \< <xref:System.Net.IPAddress> , için `IPAddress.IPv6Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen bir yerine kullanın.<br><br>Varsayılan değer: `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [Forwardedheadersdefaults varsayılan. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.<br><br>Varsayılan değer: `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen bir yerine kullanın.<br><br>Varsayılan değer: `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Bilgi işlem sayısının, işlenmekte olan [Forwardedheadersoptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitlenmiş olmasını gerektir.<br><br>ASP.NET Core 1. x içindeki varsayılan değer `true`. ASP.NET Core 2,0 veya sonraki sürümlerde varsayılan değer `false`. |

## <a name="scenarios-and-use-cases"></a>Senaryolar ve kullanım örnekleri

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>İletilen üstbilgiler eklemek mümkün olmadığında ve tüm istekler güvenlidir

Bazı durumlarda, iletilen üstbilgileri uygulamaya yönelik isteklere eklemek mümkün olmayabilir. Proxy tüm genel dış isteklerin HTTPS olduğunu zortacaktır, düzen herhangi bir tür ara yazılım kullanılmadan önce içinde `Startup.Configure` el ile ayarlanabilir:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Bu kod bir geliştirme veya hazırlama ortamındaki ortam değişkeniyle veya diğer yapılandırma ayarıyla devre dışı bırakılabilir.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Yol tabanı ve istek yolunu değiştiren proxy 'ler ile uğraşın

Bazı proxy 'ler yolu bozulmadan, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama temel yolu ile geçer. [Usepathbaseextensions. usepathbase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ara yazılımı, yolu HttpRequest. [Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve uygulama temeli yoluna, [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine böler.

`/foo` , `/foo/api/1`Olarak geçirilen bir ara sunucu yolu için uygulama temel yolu ise, ara yazılım aşağıdaki `Request.PathBase` komutla `/foo` ve `Request.Path` `/api/1` olarak ayarlanır:

```csharp
app.UsePathBase("/foo");
```

Özgün yol ve yol tabanı, ara yazılım ters ' de çağrıldığında yeniden uygulanır. Ara yazılım siparişi işleme hakkında daha fazla bilgi için <xref:fundamentals/middleware/index>bkz..

Proxy, yolu kırpar (örneğin, ' ye `/foo/api/1` `/api/1`Iletme), isteğin [pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yeniden yönlendirmeleri ve bağlantıları düzeltir:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Proxy, yol verileri ekliyor ise, kullanarak yeniden yönlendirmeleri ve bağlantıları onarmak üzere yolun bir bölümünü atın ve <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> özelliğini kullanarak yeniden yönlendirmeyi ve bağlantıları onarın:

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Farklı üstbilgi adları kullanan bir ara sunucu için yapılandırma

Proxy `X-Forwarded-For` , adlı üst bilgileri kullanmıyorsa `X-Forwarded-Proto` ve proxy adresini/bağlantı noktasını ve kaynak düzen bilgilerini iletmek için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>IPv6 adresi olarak temsil edilen IPv4 adresinin yapılandırması

Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 'da veya `::ffff:10.0.0.1` `::ffff:a00:1`olarak temsil edilen IPv4). Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.

Aşağıdaki örnekte, iletilen üstbilgiler sağlayan bir ağ adresi `KnownNetworks` listeye IPv6 biçiminde eklenir.

IPv4 adresi:`10.11.12.1/8`

Dönüştürülen IPv6 adresi:`::ffff:10.11.12.1`  
Dönüştürülen önek uzunluğu: 104

Adresi onaltılık biçimde (`10.11.12.1` IPv6 olarak `::ffff:0a0b:0c01`temsil edilir) de sağlayabilirsiniz. Bir IPv4 adresini IPv6 'ya dönüştürürken, ek`8` `::ffff:` IPv6 öneki (8 + 96 = 104) IÇIN (örnekteki) CIDR önek uzunluğuna 96 ekleyin. 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Linux ve IIS olmayan ters proxy 'ler için Düzen iletme

Bir Azure Linux <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> App Service <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> , Azure LINUX sanal makinesi (VM) veya IIS 'nin yanı sıra diğer tüm ters proxy 'ler için dağıtıldığında bir siteyi çağıran ve sonsuz döngüye alan uygulamalar. TLS, ters proxy tarafından sonlandırılır ve Kestrel doğru istek düzeninden haberdar değildir. OAuth ve OıDC yanlış yeniden yönlendirmeler oluşturduğundan bu yapılandırmada de başarısız olur. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS 'nin arkasında çalışırken Iletilen üstbilgiler ara yazılımı ekler ve yapılandırır, ancak Linux için eşleşen otomatik yapılandırma (Apache veya NGINX tümleştirmesi) yoktur.

IIS olmayan senaryolarda düzeni proxy 'den iletmek için, Iletilen üstbilgiler ara yazılımını ekleyin ve yapılandırın. İçinde `Startup.ConfigureServices`, aşağıdaki kodu kullanın:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a>Sertifika iletme 

### <a name="azure"></a>Azure

Sertifika iletme için Azure App Service yapılandırmak için, bkz. [Azure App Service IÇIN TLS karşılıklı kimlik doğrulamasını yapılandırma](/azure/app-service/app-service-web-configure-tls-mutual-auth). Aşağıdaki kılavuz ASP.NET Core uygulamasını yapılandırmaya aittir.

İçinde `Startup.Configure`, çağrısının önüne aşağıdaki kodu ekleyin `app.UseAuthentication();`:

```csharp
app.UseCertificateForwarding();
```


Sertifika Iletme ara yazılımını, Azure 'un kullandığı üst bilgi adını belirtecek şekilde yapılandırın. İçinde `Startup.ConfigureServices`, ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Diğer Web proxy 'leri

IIS veya Azure App Service uygulama Isteği yönlendirme (ARR) olmayan bir ara sunucu kullanılıyorsa, proxy 'yi bir HTTP üst bilgisinde aldığı sertifikayı iletecek şekilde yapılandırın. İçinde `Startup.Configure`, çağrısının önüne aşağıdaki kodu ekleyin `app.UseAuthentication();`:

```csharp
app.UseCertificateForwarding();
```

Üst bilgi adını belirtmek için sertifika Iletme ara yazılımını yapılandırın. İçinde `Startup.ConfigureServices`, ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Ara sunucu Base64 ile kodlanmazsa (NGINX ile aynı durumda olduğu gibi), `HeaderConverter` seçeneğini ayarlayın. İçinde `Startup.ConfigureServices`aşağıdaki örneği göz önünde bulundurun:

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a>Sorun giderme

Üstbilgiler beklendiği gibi iletilemediği zaman [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin. Günlükler sorunu gidermek için yeterli bilgi sağlamıyorsa, sunucu tarafından alınan istek üstbilgilerini numaralandırın. Bir uygulama yanıtına istek üst bilgileri yazmak veya üst bilgileri günlüğe kaydetmek için satır içi ara yazılım kullanın. 

Üst bilgileri uygulamanın yanıtına yazmak için, aşağıdaki Terminal satır içi ara yazılımını, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> içindeki `Startup.Configure`çağrısından hemen sonra yerleştirin:

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Yanıt gövdesi yerine günlüklere yazabilirsiniz. Günlüklere yazmak, sitenin hata ayıklanırken normal şekilde çalışmasına olanak tanır.

Yanıt gövdesi yerine günlükleri yazmak için:

* [Başlangıçta günlükleri oluşturma](xref:fundamentals/logging/index#create-logs-in-startup)bölümünde açıklandığı gibi `ILogger<Startup>` `Startup` sınıfına ekleyin.
* <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> İçindeki `Startup.Configure`çağrısından hemen sonra aşağıdaki satır içi ara yazılımı yerleştirin.

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler öğesine `X-Original-{For|Proto|Host}`taşınır. Belirli bir üst bilgide birden çok değer varsa, Iletilen üstbilgiler ara yazılımı sağdan sola doğru sırada üst bilgileri işler. Varsayılan `ForwardLimit` değer ( `1` bir) ise, değeri arttırılmadığı müddetçe yalnızca en `ForwardLimit` sağdaki üst bilgilerden en sağdaki değer işlenir.

İletilen üstbilgiler işlenmeden önce isteğin özgün uzak IP 'si `KnownProxies` veya `KnownNetworks` listelerindeki bir girdiyle eşleşmesi gerekir. Bu, güvenilmeyen proxy 'lerden ileticileri kabul etmediği için üst bilgi yanıltmasını kısıtlar. Bilinmeyen bir proxy algılandığında günlüğe kaydetme, proxy 'nin adresini gösterir:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Yukarıdaki örnekte, alana 10.0.0.100 bir proxy sunucusudur. Sunucu, güvenilir bir ara sunucu ise, sunucusunun IP adresini ' `KnownProxies` `KnownNetworks` `Startup.ConfigureServices`e (veya güvenilen bir ağı öğesine ekleyin) ekleyin. Daha fazla bilgi için [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Yalnızca güvenilen proxy 'lerin ve ağların üstbilgileri iletmesine izin verir. Aksi takdirde, [IP sahtekarlığı](https://www.iplocation.net/ip-spoofing) saldırıları mümkündür.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/web-farm>
* [Microsoft güvenlik danışmanlık CVE-2018-0787: ayrıcalık yükselmesi güvenlik açığı ASP.NET Core](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core için Önerilen yapılandırmada, uygulama IIS/ASP. NET Core modülü, NGINX veya Apache kullanılarak barındırılır. Proxy sunucuları, yük dengeleyiciler ve diğer ağ gereçleri, genellikle istek hakkında uygulamaya ulaşmadan önce bu bilgileri gizlemediğinde:

* HTTPS istekleri HTTP üzerinden proxy olduğunda, özgün düzen (HTTPS) kaybolur ve bir üst bilgide iletilmesi gerekir.
* Bir uygulama, Internet veya şirket ağında gerçek kaynağını değil, ara sunucudan bir istek aldığından, kaynak istemci IP adresinin de bir üst bilgide iletilmesi gerekir.

Bu bilgiler, istek işlemede, örneğin yeniden yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konum gibi önemli olabilir.

## <a name="forwarded-headers"></a>İletilen üstbilgiler

Kurala göre, proxy 'leri HTTP başlıklarında iletme bilgileri.

| Üst bilgi | Açıklama |
| ------ | ----------- |
| X-Iletilmiş-Için | , İsteği başlatan istemciyle ilgili bilgileri ve bir proxy zincirinde sonraki proxy 'leri barındırır. Bu parametre IP adresleri (ve isteğe bağlı olarak bağlantı noktası numaraları) içerebilir. Bir ara sunucu zincirinde, ilk parametre isteğin ilk yaptığı istemciyi gösterir. Sonraki proxy tanımlayıcıları izler. Zincirdeki son proxy, Parametreler listesinde değil. Son proxy 'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanında uzak IP adresi olarak kullanılabilir. |
| X-Iletilen-proto | Kaynak düzenin değeri (HTTP/HTTPS). Bu değer, istek birden çok proxy 'ye geçen bir düzen listesi de olabilir. |
| X-Iletilen-konak | Ana bilgisayar üst bilgisi alanının özgün değeri. Genellikle, proxy 'ler ana bilgisayar üst bilgisini değiştirmez. Proxy 'nin konak üstbilgilerini doğrulamadığı veya kısıtlayamayacağı sistemleri etkileyen ayrıcalık yükselmesi güvenlik açığı hakkında bilgi için bkz. [Microsoft Güvenlik Danışmanlığı CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) . |

[Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki Iletilen üstbilgiler ara yazılımı, bu üst bilgileri okur ve ilgili alanları üzerinde <xref:Microsoft.AspNetCore.Http.HttpContext>doldurur.

Ara yazılım güncelleştirmeleri:

* Üst bilgi değerini kullanarak [HttpContext. Connection. remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; kümesi. `X-Forwarded-For` Ek ayarlar, ara yazılımın nasıl ayarlanacağını `RemoteIpAddress`etkiler. Ayrıntılar için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options).
* Üst bilgi değerini kullanan [HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; kümesi. `X-Forwarded-Proto`
* Üst bilgi değerini kullanan [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; kümesi. `X-Forwarded-Host`

İletilen üstbilgiler ara yazılımı [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir. Varsayılan ayarlar şunlardır:

* Uygulama ve isteklerin kaynağı arasında yalnızca *bir ara sunucu* vardır.
* Bilinen proxy 'ler ve bilinen ağlar için yalnızca geri döngü adresleri yapılandırılır.
* İletilen üstbilgiler ve `X-Forwarded-Proto`olarak adlandırılır `X-Forwarded-For` .

Tüm ağ gereçleri ek yapılandırma olmadan `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini eklemez. Proxy istekler uygulamaya ulaştığında bu üst bilgileri içermiyorsa, Gereç üreticinizin kılavuzuna başvurun. Gereç `X-Forwarded-For` , ve `X-Forwarded-Proto`dışındaki farklı üstbilgi adlarını kullanıyorsa, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini gereç tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın. Daha fazla bilgi için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) ve [farklı üstbilgi adları kullanan bir proxy için yapılandırma](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS/IIS Express ve ASP.NET Core modülü

İletilen üstbilgiler ara yazılımı, uygulama IIS 'nin arkasında ve ASP.NET Core modülünden sonra [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) barındırılıyorsa [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından varsayılan olarak etkinleştirilir. İletilen üstbilgiler ara yazılımı, iletilen üst bilgilerle (örneğin, [IP aldatmacası](https://www.iplocation.net/ip-spoofing)) güven sorunları nedeniyle ASP.NET Core modülüne özgü kısıtlanmış bir yapılandırmaya sahip olan ara yazılım ardışık düzeninde ilk olarak çalışır. Ara yazılım, `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini iletmek üzere yapılandırılmıştır ve tek bir localhost proxy ile kısıtlanır. Ek yapılandırma gerekliyse, [Iletilen üstbilgiler ara yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Diğer proxy sunucu ve yük dengeleyici senaryoları

[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında [IIS tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 'nin kullanılması dışında, iletilen üstbilgiler ara yazılımı varsayılan olarak etkinleştirilmemiştir. İletilen üstbilgileri ile <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>işlemek için bir uygulamanın yönlendirilmiş üst bilgi ara yazılımı etkinleştirilmelidir. Ara yazılım için Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse, ara yazılımı etkinleştirdikten sonra varsayılan [Forwardedheadersoptions. Forwardedheaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) , [forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dir.

İle <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ara yazılımı, içindeki `X-Forwarded-For` `X-Forwarded-Proto` `Startup.ConfigureServices`ve üst bilgilerini iletecek şekilde yapılandırın. Diğer ara <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> yazılım çağrılmadan `Startup.Configure` önce metodunu çağırın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `Startup.ConfigureServices` veya doğrudan genişletme yöntemine belirtilmemişse <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, Iletmek için varsayılan üstbilgiler [forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Özelliğin, iletmek için üst bilgilerle yapılandırılması gerekir.

## <a name="nginx-configuration"></a>NGINX yapılandırması

`X-Forwarded-For` Ve `X-Forwarded-Proto` üst bilgilerini iletmek için bkz <xref:host-and-deploy/linux-nginx#configure-nginx>.. Daha fazla bilgi için bkz. [NGINX: iletilen üstbilgiyi kullanma](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Apache yapılandırması

`X-Forwarded-For`otomatik olarak eklenir (bkz. [Apache modülü mod_proxy: ters proxy Istek üstbilgileri](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). `X-Forwarded-Proto` Üstbilgiyi iletme hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-apache#configure-apache>..

## <a name="forwarded-headers-middleware-options"></a>İletilen üstbilgiler ara yazılım seçenekleri

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen üstbilgiler ara yazılımı davranışını denetleyin. Aşağıdaki örnek varsayılan değerleri değiştirir:

* İletilen üst bilgilerdeki giriş sayısını sınırlayın `2`.
* Bilinen bir proxy adresi ekleyin `127.0.10.1`.
* İletilen üst bilgi adını varsayılan `X-Forwarded-For` değerinden olarak `X-Forwarded-For-My-Custom-Header-Name`değiştirin.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Seçenek | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ana bilgisayarları `X-Forwarded-Host` üst bilgiyle belirtilen değerlerle sınırlandırır.<ul><li>Değerler, Ordinal-Ignore-Case kullanılarak karşılaştırılır.</li><li>Bağlantı noktası numaraları dışlanmalıdır.</li><li>Liste boşsa, tüm konaklara izin verilir.</li><li>Üst düzey joker karakter `*` boş olmayan tüm konaklara izin verir.</li><li>Alt etki alanı Joker karakterlere izin verilir ancak kök etki alanıyla eşleşmez. Örneğin, `*.contoso.com` kök etki alanını değil `foo.contoso.com` , alt etki alanıyla `contoso.com`eşleşir.</li><li>Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [puni koduna](https://tools.ietf.org/html/rfc3492) dönüştürülür.</li><li>[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı ayraçları içermeli ve [geleneksel biçimde](https://tools.ietf.org/html/rfc4291#section-2.2) olmalıdır (örneğin, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). IPv6 adresleri, farklı biçimler arasında mantıksal eşitlik denetimi için özel değildir ve hiçbir kurallı kullanım yapılmaz.</li><li>İzin verilen konaklar kısıtlanamaması, bir saldırganın hizmet tarafından oluşturulan bağlantıları sızdırmasına izin verebilir.</li></ul>Varsayılan değer boş `IList<string>`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen bir yerine kullanın. Bu seçenek, proxy/iletici `X-Forwarded-For` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Hangi ileticilerin işleneceğini tanımlar. Uygulanan alanların listesi için [Forwardedheaders numaralandırması](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) ' ne bakın. Bu özelliğe atanan tipik değerler şunlardır `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Varsayılan değer [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [Forwardedheadersdefaults varsayılan. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın. Bu seçenek, proxy/iletici `X-Forwarded-Host` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen bir yerine kullanın. Bu seçenek, proxy/iletici `X-Forwarded-Proto` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | İşlenen üst bilgilerdeki giriş sayısını sınırlandırır. Sınırı devre `null` dışı bırakmak için olarak ayarlayın, ancak bu yalnızca `KnownProxies` veya `KnownNetworks` yapılandırılmışsa yapılmalıdır. `null` Değer olmayan bir önlem, hatalı yapılandırılmış proxy 'lerde ve ağdaki yan kanallardan gelen kötü amaçlı isteklere karşı koruma sağlamak için bir önlem (garanti değil) olarak ayarlanır.<br><br>İletilen üstbilgiler ara yazılımı, üst bilgileri sağdan sola doğru sırada işler. Varsayılan değer (`1`) kullanılırsa, değeri `ForwardLimit` artmadığı müddetçe yalnızca üst bilgilerden en sağdaki değer işlenir.<br><br>Varsayılan değer: `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | İletilen üstbilgileri kabul etmek için bilinen ağların adres aralıkları. Sınıfsız Etki alanları arası yönlendirme (CıDR) gösterimini kullanarak IP aralıkları sağlayın.<br><br>Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak `::ffff:10.0.0.1`temsil edilen IPv4 içinde). Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın. Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.<br><br>Varsayılan `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> , için `IPAddress.Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | İletilen üstbilgileri kabul etmek için bilinen proxy 'lerin adresleri. Tam `KnownProxies` IP adresi eşleşmelerini belirtmek için kullanın.<br><br>Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak `::ffff:10.0.0.1`temsil edilen IPv4 içinde). Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın. Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.<br><br>Varsayılan `IList` \< <xref:System.Net.IPAddress> , için `IPAddress.IPv6Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen bir yerine kullanın.<br><br>Varsayılan değer: `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [Forwardedheadersdefaults varsayılan. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.<br><br>Varsayılan değer: `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen bir yerine kullanın.<br><br>Varsayılan değer: `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Bilgi işlem sayısının, işlenmekte olan [Forwardedheadersoptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitlenmiş olmasını gerektir.<br><br>ASP.NET Core 1. x içindeki varsayılan değer `true`. ASP.NET Core 2,0 veya sonraki sürümlerde varsayılan değer `false`. |

## <a name="scenarios-and-use-cases"></a>Senaryolar ve kullanım örnekleri

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>İletilen üstbilgiler eklemek mümkün olmadığında ve tüm istekler güvenlidir

Bazı durumlarda, iletilen üstbilgileri uygulamaya yönelik isteklere eklemek mümkün olmayabilir. Proxy tüm genel dış isteklerin HTTPS olduğunu zortacaktır, düzen herhangi bir tür ara yazılım kullanılmadan önce içinde `Startup.Configure` el ile ayarlanabilir:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Bu kod bir geliştirme veya hazırlama ortamındaki ortam değişkeniyle veya diğer yapılandırma ayarıyla devre dışı bırakılabilir.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Yol tabanı ve istek yolunu değiştiren proxy 'ler ile uğraşın

Bazı proxy 'ler yolu bozulmadan, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama temel yolu ile geçer. [Usepathbaseextensions. usepathbase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ara yazılımı, yolu HttpRequest. [Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve uygulama temeli yoluna, [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine böler.

`/foo` , `/foo/api/1`Olarak geçirilen bir ara sunucu yolu için uygulama temel yolu ise, ara yazılım aşağıdaki `Request.PathBase` komutla `/foo` ve `Request.Path` `/api/1` olarak ayarlanır:

```csharp
app.UsePathBase("/foo");
```

Özgün yol ve yol tabanı, ara yazılım ters ' de çağrıldığında yeniden uygulanır. Ara yazılım siparişi işleme hakkında daha fazla bilgi için <xref:fundamentals/middleware/index>bkz..

Proxy, yolu kırpar (örneğin, ' ye `/foo/api/1` `/api/1`Iletme), isteğin [pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yeniden yönlendirmeleri ve bağlantıları düzeltir:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Proxy, yol verileri ekliyor ise, kullanarak yeniden yönlendirmeleri ve bağlantıları onarmak üzere yolun bir bölümünü atın ve <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> özelliğini kullanarak yeniden yönlendirmeyi ve bağlantıları onarın:

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Farklı üstbilgi adları kullanan bir ara sunucu için yapılandırma

Proxy `X-Forwarded-For` , adlı üst bilgileri kullanmıyorsa `X-Forwarded-Proto` ve proxy adresini/bağlantı noktasını ve kaynak düzen bilgilerini iletmek için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>IPv6 adresi olarak temsil edilen IPv4 adresinin yapılandırması

Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 'da veya `::ffff:10.0.0.1` `::ffff:a00:1`olarak temsil edilen IPv4). Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.

Aşağıdaki örnekte, iletilen üstbilgiler sağlayan bir ağ adresi `KnownNetworks` listeye IPv6 biçiminde eklenir.

IPv4 adresi:`10.11.12.1/8`

Dönüştürülen IPv6 adresi:`::ffff:10.11.12.1`  
Dönüştürülen önek uzunluğu: 104

Adresi onaltılık biçimde (`10.11.12.1` IPv6 olarak `::ffff:0a0b:0c01`temsil edilir) de sağlayabilirsiniz. Bir IPv4 adresini IPv6 'ya dönüştürürken, ek`8` `::ffff:` IPv6 öneki (8 + 96 = 104) IÇIN (örnekteki) CIDR önek uzunluğuna 96 ekleyin. 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Linux ve IIS olmayan ters proxy 'ler için Düzen iletme

Bir Azure Linux <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> App Service <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> , Azure LINUX sanal makinesi (VM) veya IIS 'nin yanı sıra diğer tüm ters proxy 'ler için dağıtıldığında bir siteyi çağıran ve sonsuz döngüye alan uygulamalar. TLS, ters proxy tarafından sonlandırılır ve Kestrel doğru istek düzeninden haberdar değildir. OAuth ve OıDC yanlış yeniden yönlendirmeler oluşturduğundan bu yapılandırmada de başarısız olur. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS 'nin arkasında çalışırken Iletilen üstbilgiler ara yazılımı ekler ve yapılandırır, ancak Linux için eşleşen otomatik yapılandırma (Apache veya NGINX tümleştirmesi) yoktur.

IIS olmayan senaryolarda düzeni proxy 'den iletmek için, Iletilen üstbilgiler ara yazılımını ekleyin ve yapılandırın. İçinde `Startup.ConfigureServices`, aşağıdaki kodu kullanın:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a>Sorun giderme

Üstbilgiler beklendiği gibi iletilemediği zaman [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin. Günlükler sorunu gidermek için yeterli bilgi sağlamıyorsa, sunucu tarafından alınan istek üstbilgilerini numaralandırın. Bir uygulama yanıtına istek üst bilgileri yazmak veya üst bilgileri günlüğe kaydetmek için satır içi ara yazılım kullanın. 

Üst bilgileri uygulamanın yanıtına yazmak için, aşağıdaki Terminal satır içi ara yazılımını, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> içindeki `Startup.Configure`çağrısından hemen sonra yerleştirin:

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Yanıt gövdesi yerine günlüklere yazabilirsiniz. Günlüklere yazmak, sitenin hata ayıklanırken normal şekilde çalışmasına olanak tanır.

Yanıt gövdesi yerine günlükleri yazmak için:

* [Başlangıçta günlükleri oluşturma](xref:fundamentals/logging/index#create-logs-in-startup)bölümünde açıklandığı gibi `ILogger<Startup>` `Startup` sınıfına ekleyin.
* <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> İçindeki `Startup.Configure`çağrısından hemen sonra aşağıdaki satır içi ara yazılımı yerleştirin.

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler öğesine `X-Original-{For|Proto|Host}`taşınır. Belirli bir üst bilgide birden çok değer varsa, Iletilen üstbilgiler ara yazılımı sağdan sola doğru sırada üst bilgileri işler. Varsayılan `ForwardLimit` değer ( `1` bir) ise, değeri arttırılmadığı müddetçe yalnızca en `ForwardLimit` sağdaki üst bilgilerden en sağdaki değer işlenir.

İletilen üstbilgiler işlenmeden önce isteğin özgün uzak IP 'si `KnownProxies` veya `KnownNetworks` listelerindeki bir girdiyle eşleşmesi gerekir. Bu, güvenilmeyen proxy 'lerden ileticileri kabul etmediği için üst bilgi yanıltmasını kısıtlar. Bilinmeyen bir proxy algılandığında günlüğe kaydetme, proxy 'nin adresini gösterir:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Yukarıdaki örnekte, alana 10.0.0.100 bir proxy sunucusudur. Sunucu, güvenilir bir ara sunucu ise, sunucusunun IP adresini ' `KnownProxies` `KnownNetworks` `Startup.ConfigureServices`e (veya güvenilen bir ağı öğesine ekleyin) ekleyin. Daha fazla bilgi için [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Yalnızca güvenilen proxy 'lerin ve ağların üstbilgileri iletmesine izin verir. Aksi takdirde, [IP sahtekarlığı](https://www.iplocation.net/ip-spoofing) saldırıları mümkündür.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/web-farm>
* [Microsoft güvenlik danışmanlık CVE-2018-0787: ayrıcalık yükselmesi güvenlik açığı ASP.NET Core](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
