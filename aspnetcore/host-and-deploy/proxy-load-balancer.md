---
title: Proxy sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core yapılandırma
author: rick-anderson
description: Proxy sunucuları ve yük dengeleyicileri arkasında barındırılan ve genellikle önemli istek bilgilerini gizleyen uygulamalar için yapılandırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659385"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Proxy sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core yapılandırma

Yazar: [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core için önerilen yapılandırmada uygulama IIS/ASP.NET Core Module, Nginx veya Apache kullanılarak barındırılır. Proxy sunucular, yük dengeleyicileri ve diğer ağ cihazları genellikle uygulamaya ulaşmadan önce istek le ilgili bilgileri belirsiz hale getirir:

* HTTPS istekleri HTTP üzerinden iliştirildiğinde, özgün düzen (HTTPS) kaybolur ve bir üstbilgide iletilmelidir.
* Bir uygulama, Internet veya şirket ağındaki gerçek kaynağından değil, proxy'den bir istek aldığından, kaynak olan istemci IP adreside de üstbilgide iletilmesi gerekir.

Bu bilgiler, yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konumlandırma gibi istek işlemede önemli olabilir.

## <a name="forwarded-headers"></a>İleriye dönük üstbilgi

Sözleşmeye göre, vekiller BILGILERI HTTP üstbilgilerine iletir.

| Üst bilgi | Açıklama |
| ------ | ----------- |
| X-Forwarded-For | İsteği başlatan istemci ve sonraki yakınlıklar hakkında bilgileri bir yakınlık lar zincirinde tutar. Bu parametre IP adresleri (ve isteğe bağlı olarak, bağlantı noktası numaraları) içerebilir. Proxy sunucuları zincirinde, ilk parametre isteğiilk yapıldığı istemciyi gösterir. Sonraki proxy tanımlayıcıları izleyin. Zincirdeki son proxy parametreler listesinde değil. Son proxy'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanındaki uzak IP adresi olarak kullanılabilir. |
| X-İlli-Proto | Kaynak düzenin in değeri (HTTP/HTTPS). İstek birden çok yakınlık arasında geçtiyse, değer de şemaları listesi olabilir. |
| X-Forwarded-Host | Ana Bilgisayar üstbilgi alanının özgün değeri. Genellikle, yakınlık ana bilgisayar üstbilgisini değiştirmez. Proxy'nin Ana Bilgisayar üstbilgilerini doğrulayamadığı veya bilinen iyi değerlerle sınırlamadığı sistemleri etkileyen ayrıcalıkların yükseltisi güvenlik açığı hakkında bilgi almak için [Microsoft Güvenlik Danışma Belgesi CVE-2018-0787'ye](https://github.com/aspnet/Announcements/issues/295) bakın. |

[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden Iletilen Üstbilgi Middleware, bu başlıkları okur ve ilgili <xref:Microsoft.AspNetCore.Http.HttpContext>alanları doldurur.

Ara yazılım güncellemeleri:

* Başlık değerini kullanarak `X-Forwarded-For` [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set. Ek ayarlar, ara yazılımın nasıl ayarlanır' ı `RemoteIpAddress`etkiler. Ayrıntılar [için, İlezli Üstbilgiler Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.
* Başlık değerini kullanarak `X-Forwarded-Proto` [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set.
* Başlık değerini kullanarak `X-Forwarded-Host` [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set.

İlezli Üstbilgi Middleware [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir. Varsayılan ayarlar şunlardır:

* Uygulama ve isteklerin kaynağı arasında yalnızca *bir proxy* vardır.
* Yalnızca geri dönüş adresleri bilinen yakınlıklar ve bilinen ağlar için yapılandırılır.
* İlerlenen üstbilgi `X-Forwarded-For` adlandırılmış ve `X-Forwarded-Proto`.

Tüm ağ cihazları `X-Forwarded-For` ek `X-Forwarded-Proto` yapılandırma olmadan üstbilgi ve üstbilgi eklemez. Yakın istekleri uygulamaya ulaştıklarında bu üstbilgileri içermiyorsa, cihaz üreticinizin kılavuzuna başvurun. Cihaz, cihazdan `X-Forwarded-For` farklı üstbilgi `X-Forwarded-Proto`adları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> kullanıyorsa ve cihaz tarafından kullanılan üstbilgi adlarıyla eşleşecek <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> şekilde ve seçenekleri ayarlayın. Daha fazla bilgi için, [farklı üstbilgi adları kullanan bir proxy için](#configuration-for-a-proxy-that-uses-different-header-names) [Iletili Üstbilgiler Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) ve Yapılandırma'ya bakın.

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS/IIS Express ve ASP.NET Çekirdek Modülü

İleşli Üstbilgi Araware, uygulama IIS ve ASP.NET Çekirdek Modülü' nün arkasında [sürücü](xref:host-and-deploy/iis/index#out-of-process-hosting-model) lük sınır dışında barındırılırken Varsayılan olarak [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından etkinleştirilir. İleli Üstbilgi Middleware, iletili üstdeğerlere [(örneğin, IP sızdırma)](https://www.iplocation.net/ip-spoofing)güven endişeleri nedeniyle ASP.NET Çekirdek Modülüne özgü sınırlı bir yapılandırmaile ilk olarak ara yazılım boru hattında çalışacak şekilde etkinleştirilir. Ara yazılım, üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi ileri ye doğru yapılandırılır ve tek bir localhost proxy ile sınırlıdır. Ek yapılandırma gerekiyorsa, [İleriye Dönük Üstbilgi Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Diğer proxy sunucusu ve yük dengeleyici senaryoları

[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma yaparken [IIS Tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) kullanmanın dışında, Iletili Üstbilgi Middleware varsayılan olarak etkinleştirilmez. İleşli Üstbilgi Ara ware, bir uygulamanın <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>iletilir üstbilgisini ' ile işlemesi için etkinleştirilmelidir. Middleware'e hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse ara yazılımı etkinleştirdikten sonra, varsayılan [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) [forwardedheaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Aracı, 'deki <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` üstbilgi ve `X-Forwarded-Proto` üstileri `Startup.ConfigureServices`iletmek için yapılandırın. Diğer <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> ara `Startup.Configure` yazılımları çağırmadan önce yöntemi çağırın:

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
> Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> veya doğrudan `Startup.ConfigureServices` uzantısı yöntemi nde <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>belirtilmişise , ileri varsayılan üstbilgi [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)vardır. Özellik <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> ileri ye doğru üstbilgi ile yapılandırılmalıdır.

## <a name="nginx-configuration"></a>Nginx yapılandırması

Üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi <xref:host-and-deploy/linux-nginx#configure-nginx>ileri almak için bkz. Daha fazla bilgi için [NGINX: İleride üstbilgiyi kullanma.](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)

## <a name="apache-configuration"></a>Apache yapılandırması

`X-Forwarded-For`otomatik olarak eklenir [(bkz. Apache Modülü mod_proxy: Proxy İstek Üstbilgilerini Ters](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)Çevir ). Üstbilginin nasıl iletilen `X-Forwarded-Proto` <xref:host-and-deploy/linux-apache#configure-apache>hakkında bilgi için bkz.

## <a name="forwarded-headers-middleware-options"></a>İleşli Üstbilgi Ara ware seçenekleri

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen Üstbilgi Middleware davranışını kontrol edin. Aşağıdaki örnekte varsayılan değerleri değiştirir:

* İlerlenen üstbilgideki giriş sayısını `2`' la sınırlandırın.
* `127.0.10.1`Bilinen bir proxy adresi ekleyin.
* İdeyiş üstbilgi adını `X-Forwarded-For` varsayılandan ' a `X-Forwarded-For-My-Custom-Header-Name`çevirin.

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
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ana bilgisayarları `X-Forwarded-Host` üstbilgi yle sağlanan değerlerle sınırlandırıyor.<ul><li>Değerler, ordinal-ignore-case kullanılarak karşılaştırılır.</li><li>Bağlantı noktası numaraları hariç tutulmalıdır.</li><li>Liste boşsa, tüm ana bilgisayarlara izin verilir.</li><li>Üst düzey joker `*` karakter, boş olmayan tüm ana bilgisayarları sağlar.</li><li>Alt etki alanı joker kartlarına izin verilir, ancak kök etki alanıyla eşleşmez. Örneğin, `*.contoso.com` alt etki `foo.contoso.com` alanı eşleşir, `contoso.com`ancak kök etki alanı .</li><li>Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [Punycode'a](https://tools.ietf.org/html/rfc3492) dönüştürülür.</li><li>[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı parantezler içermelidir ve [geleneksel](https://tools.ietf.org/html/rfc4291#section-2.2) formda `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`olmalıdır (örneğin, ). IPv6 adresleri, farklı biçimler arasındaki mantıksal eşitliği denetlemek için özel olarak belirtilmemiştir ve hiçbir kanonializasyon gerçekleştirilmez.</li><li>İzin verilen ana bilgisayarların kısıtlanmasına izin verilmese, saldırgan hizmet tarafından oluşturulan bağlantıları sızdırmasına neden olabilir.</li></ul>Varsayılan değer boştur. `IList<string>` |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın. Bu seçenek, proxy/forwarder `X-Forwarded-For` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Hangi iletmelerin işlenmesi gerektiğini belirler. Geçerli alanların listesi için [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) bölümüne bakın. Bu özelliğe atanan `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tipik değerler.<br><br>Varsayılan değer [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)olduğunu. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın. Bu seçenek, proxy/forwarder `X-Forwarded-Host` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın. Bu seçenek, proxy/forwarder `X-Forwarded-Proto` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | İşlenen üstbilgideki giriş sayısını sınırlar. Sınırı devre dışı bırakacak şekilde ayarlayın, ancak `KnownProxies` bu `KnownNetworks` yalnızca yapılandırıldıysa veya yapılandırılırsa yapılmalıdır. `null` `null` Değer olmayan bir ayar, ağdaki yan kanallardan gelen yanlış yapılandırılmış yardımcılara ve kötü amaçlı isteklere karşı korunmak için bir önlemdir (ancak garanti değildir).<br><br>İlli Üstbilgi Middleware, üstleri sağdan sola doğru ters sırada işler. Varsayılan değer (`1`) kullanılırsa, değeri `ForwardLimit` artırılmadığı sürece yalnızca üstbilgiden en doğru değer işlenir.<br><br>Varsayılan değer: `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | İleriye dönük üstbilgileri kabul etmek için bilinen ağların adres aralıkları. Sınıfsız Etki Alanı Yönlendirme (CIDR) gösterimini kullanarak IP aralıkları sağlayın.<br><br>Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir). Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin. Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.<br><br>Varsayılan değer, `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> '' için `IPAddress.Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | İleriye dönük üstbilgi kabul etmek için bilinen yakınlık adresleri. Tam `KnownProxies` IP adresi eşleşmelerini belirtmek için kullanın.<br><br>Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir). Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin. Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.<br><br>Varsayılan değer, `IList` \< <xref:System.Net.IPAddress> '' için `IPAddress.IPv6Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.<br><br>Varsayılan değer: `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.<br><br>Varsayılan değer: `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.<br><br>Varsayılan değer: `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Üstbilgi değerlerinin sayısının, işlenen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitolmasını zorunlu kılmasını.<br><br>Core 1.x ASP.NET varsayılan `true`. Core 2.0 veya sonraki ASP.NET `false`varsayılan. |

## <a name="scenarios-and-use-cases"></a>Senaryolar ve kullanım örnekleri

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>İleiletiüstleri eklemek mümkün olmadığında ve tüm isteklerin güvenli olduğu

Bazı durumlarda, uygulamaya iledilen üstbilgi eklemek mümkün olmayabilir. Proxy tüm genel dış isteklerin HTTPS olduğunu zorluyorsa, şema `Startup.Configure` herhangi bir ara yazılım türü kullanmadan önce el ile ayarlanabilir:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Bu kod, bir geliştirme veya evreleme ortamında bir ortam değişkeni veya başka bir yapılandırma ayarı ile devre dışı alınabilir.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>İstek yolunu değiştiren yol tabanı ve yakınlıklarla başa çık

Bazı yakınlıklar yolu sağlam bir şekilde geçer, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama tabanı yolu ile. [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware [httprequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine uygulama temel yolu içine yol böler.

Proxy `/foo` `/foo/api/1`yolu olarak geçirilen bir proxy yolu için uygulama `Request.PathBase` `/foo` temel `Request.Path` `/api/1` yolu ise, ara yazılım aşağıdaki komutla ve ayarlar:

```csharp
app.UsePathBase("/foo");
```

Orta yazılım ters olarak yeniden çağrıldığında özgün yol ve yol tabanı yeniden uygulanır. Ara yazılım siparişi işleme hakkında <xref:fundamentals/middleware/index>daha fazla bilgi için bkz.

Proxy yolu kırparsa (örneğin, `/foo/api/1` `/api/1`iletilmesi), isteğin [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yönlendirmeleri ve bağlantıları düzeltin:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Proxy yol verileri ekliyorsa, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> özelliği kullanarak ve atayarak yeniden yönlendirmeleri ve bağlantıları düzeltmek için yolun bir kısmını atın:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Farklı üstbilgi adları kullanan bir proxy için yapılandırma

Proxy `X-Forwarded-For` adlandırılmış üstbilgileri kullanmıyorsa `X-Forwarded-Proto` ve proxy adresi/bağlantı noktası ve kaynak <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> düzeni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> bilgilerini iletmek için, proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ve seçenekleri ayarlayın:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>IPv6 adresi olarak temsil edilen bir IPv4 adresi için yapılandırma

Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1` IPv6 olarak veya `::ffff:a00:1`olarak temsil edilir). Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.

Aşağıdaki örnekte, iletilen üstbilgileri sağlayan bir ağ `KnownNetworks` adresi IPv6 biçiminde listeye eklenir.

IPv4 adresi:`10.11.12.1/8`

Dönüştürülmüş IPv6 adresi:`::ffff:10.11.12.1`  
Dönüştürülmüş önek uzunluğu: 104

Adresi hexadecimal formatta da sağlayabilirsiniz (IPv6'da`10.11.12.1` temsil `::ffff:0a0b:0c01`edilir). Bir IPv4 adresini IPv6'ya dönüştürürken, ek`8` `::ffff:` IPv6 önekini (8 + 96 = 104) hesaba katmak için CIDR Önek Uzunluğu'na (örnekte) 96 ekleyin. 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Linux ve IIS olmayan ters vekiller için şemayı iletin

Bir Azure <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Linux Uygulama Hizmetine, Azure Linux sanal makinesine (VM) veya IIS dışındaki diğer ters proxy'nin arkasına dağıtılırsa, bir siteyi arayan ve sonsuz bir döngüye sokan uygulamalar. TLS ters proxy tarafından sonlandırılır ve Kestrel doğru istek şeması ndan haberdar yapılmaz. OAuth ve OIDC de yanlış yönlendirmeler oluşturdukları için bu yapılandırmada başarısız olurlar. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS'nin arkasında çalışırken İleriye Dönük Üstbilgi Middleware ekler ve yapılandırır, ancak Linux için eşleşen bir otomatik yapılandırma yoktur (Apache veya Nginx entegrasyonu).

Düzeni IIS olmayan senaryolarda proxy'den iletmek için, İleriye Dönük Üstbilgi Ara Ware ekleyin ve yapılandırın. In `Startup.ConfigureServices`, aşağıdaki kodu kullanın:

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

Sertifika iletme için Azure Uygulama Hizmetini yapılandırmak [için](/azure/app-service/app-service-web-configure-tls-mutual-auth)bkz. Aşağıdaki kılavuz, ASP.NET Core uygulamasının yapılandırılmasıyla ilgilidir.

In `Startup.Configure`, aramadan önce aşağıdaki `app.UseAuthentication();`kodu ekleyin:

```csharp
app.UseCertificateForwarding();
```


Azure'un kullandığı üstbilgi adını belirtmek için Sertifika Iletme Ara ware'i yapılandırın. In `Startup.ConfigureServices`, ara yazılımın bir sertifika oluşturduğu üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Diğer web yakınlıkları

IIS veya Azure Uygulama Hizmetinin Uygulama İsteği Yönlendirmesi (ARR) olmayan bir proxy kullanılıyorsa, proxy'yi http üstbilgisinde aldığı sertifikayı iletmek için yapılandırın. In `Startup.Configure`, aramadan önce aşağıdaki `app.UseAuthentication();`kodu ekleyin:

```csharp
app.UseCertificateForwarding();
```

Üstbilgi adını belirtmek için Sertifika Iletme Aracı'nı yapılandırın. In `Startup.ConfigureServices`, ara yazılımın bir sertifika oluşturduğu üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Proxy base64-kodlama sertifikası (Nginx ile olduğu gibi) değilse, `HeaderConverter` seçeneği ayarlayın. Aşağıdaki örneği göz `Startup.ConfigureServices`önünde bulundurun:

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

Üstbilgi beklendiği gibi iletilmediğinde, [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin. Günlükler sorunu gidermek için yeterli bilgi sağlamazsa, sunucu tarafından alınan istek üstbilgilerini kaydedin. İstek üstbilgilerini bir uygulama yanıtına yazmak veya üstbilgigünlerini günlüğe kaydetmek için satır içi ara yazılım kullanın. 

Üstbilgileri uygulamanın yanıtına yazmak için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> aşağıdaki terminal satır ara sını aşağıdaki `Startup.Configure`çağrıdan hemen sonra yerleştirin:

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

Yanıt gövdesi yerine günlüklere yazabilirsiniz. Günlüklere yazma, hata ayıklama sırasında sitenin normal çalışmasını sağlar.

Yanıt gövdesiyerine günlükyazmak için:

* Başlangıç'ta `Startup` create [günlüklerinde](xref:fundamentals/logging/index#create-logs-in-startup)açıklandığı gibi sınıfa enjekte edin. `ILogger<Startup>`
* Aşağıdaki satır lı ara yazılımı aramadan <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`hemen sonra yerleştirin.

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

İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler ' `X-Original-{For|Proto|Host}`e taşınır. Belirli bir üstbilgide birden çok değer varsa, İlliyeli Üstbilgi Orta Ware üstileri sağdan sola doğru ters sırada işler. Varsayılan `ForwardLimit` `1` değer (bir), bu nedenle değeri `ForwardLimit` artırılmadığı sürece üstbilgiden yalnızca en doğru değer işlenir.

İdegöreli üstbilgi işlenmeden önce `KnownProxies` `KnownNetworks` isteğin orijinal uzak IP'si veya listelerdeki bir girişle eşleşmelidir. Bu, ileticileri güvenilmeyen vekillerden kabul etmeyerak başlık sızdırmayı sınırlar. Bilinmeyen bir proxy algılandığında, günlüğe kaydetme proxy'nin adresini gösterir:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Önceki örnekte, 10.0.0.100 bir proxy sunucusudur. Sunucu güvenilir bir proxy ise, sunucunun IP adresini `KnownProxies` ekleyin (veya güvenilir `KnownNetworks`bir `Startup.ConfigureServices`ağ ekleyin) içinde. Daha fazla bilgi [için, İlliyeli Üstbilgi Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Yalnızca güvenilen yakınlık ve ağların üstbilgi ilerisine girmesine izin verin. Aksi takdirde, [IP sızdırma saldırıları mümkündür.](https://www.iplocation.net/ip-spoofing)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787: ASP.NET Temel Ayrıcalık Yüksekliği Güvenlik Açığı](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core için önerilen yapılandırmada uygulama IIS/ASP.NET Core Module, Nginx veya Apache kullanılarak barındırılır. Proxy sunucular, yük dengeleyicileri ve diğer ağ cihazları genellikle uygulamaya ulaşmadan önce istek le ilgili bilgileri belirsiz hale getirir:

* HTTPS istekleri HTTP üzerinden iliştirildiğinde, özgün düzen (HTTPS) kaybolur ve bir üstbilgide iletilmelidir.
* Bir uygulama, Internet veya şirket ağındaki gerçek kaynağından değil, proxy'den bir istek aldığından, kaynak olan istemci IP adreside de üstbilgide iletilmesi gerekir.

Bu bilgiler, yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konumlandırma gibi istek işlemede önemli olabilir.

## <a name="forwarded-headers"></a>İleriye dönük üstbilgi

Sözleşmeye göre, vekiller BILGILERI HTTP üstbilgilerine iletir.

| Üst bilgi | Açıklama |
| ------ | ----------- |
| X-Forwarded-For | İsteği başlatan istemci ve sonraki yakınlıklar hakkında bilgileri bir yakınlık lar zincirinde tutar. Bu parametre IP adresleri (ve isteğe bağlı olarak, bağlantı noktası numaraları) içerebilir. Proxy sunucuları zincirinde, ilk parametre isteğiilk yapıldığı istemciyi gösterir. Sonraki proxy tanımlayıcıları izleyin. Zincirdeki son proxy parametreler listesinde değil. Son proxy'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanındaki uzak IP adresi olarak kullanılabilir. |
| X-İlli-Proto | Kaynak düzenin in değeri (HTTP/HTTPS). İstek birden çok yakınlık arasında geçtiyse, değer de şemaları listesi olabilir. |
| X-Forwarded-Host | Ana Bilgisayar üstbilgi alanının özgün değeri. Genellikle, yakınlık ana bilgisayar üstbilgisini değiştirmez. Proxy'nin Ana Bilgisayar üstbilgilerini doğrulayamadığı veya bilinen iyi değerlerle sınırlamadığı sistemleri etkileyen ayrıcalıkların yükseltisi güvenlik açığı hakkında bilgi almak için [Microsoft Güvenlik Danışma Belgesi CVE-2018-0787'ye](https://github.com/aspnet/Announcements/issues/295) bakın. |

[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden Iletilen Üstbilgi Middleware, bu başlıkları okur ve ilgili <xref:Microsoft.AspNetCore.Http.HttpContext>alanları doldurur.

Ara yazılım güncellemeleri:

* Başlık değerini kullanarak `X-Forwarded-For` [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set. Ek ayarlar, ara yazılımın nasıl ayarlanır' ı `RemoteIpAddress`etkiler. Ayrıntılar [için, İlezli Üstbilgiler Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.
* Başlık değerini kullanarak `X-Forwarded-Proto` [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set.
* Başlık değerini kullanarak `X-Forwarded-Host` [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set.

İlezli Üstbilgi Middleware [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir. Varsayılan ayarlar şunlardır:

* Uygulama ve isteklerin kaynağı arasında yalnızca *bir proxy* vardır.
* Yalnızca geri dönüş adresleri bilinen yakınlıklar ve bilinen ağlar için yapılandırılır.
* İlerlenen üstbilgi `X-Forwarded-For` adlandırılmış ve `X-Forwarded-Proto`.

Tüm ağ cihazları `X-Forwarded-For` ek `X-Forwarded-Proto` yapılandırma olmadan üstbilgi ve üstbilgi eklemez. Yakın istekleri uygulamaya ulaştıklarında bu üstbilgileri içermiyorsa, cihaz üreticinizin kılavuzuna başvurun. Cihaz, cihazdan `X-Forwarded-For` farklı üstbilgi `X-Forwarded-Proto`adları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> kullanıyorsa ve cihaz tarafından kullanılan üstbilgi adlarıyla eşleşecek <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> şekilde ve seçenekleri ayarlayın. Daha fazla bilgi için, [farklı üstbilgi adları kullanan bir proxy için](#configuration-for-a-proxy-that-uses-different-header-names) [Iletili Üstbilgiler Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) ve Yapılandırma'ya bakın.

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS/IIS Express ve ASP.NET Çekirdek Modülü

İleşli Üstbilgi Araware, uygulama IIS ve ASP.NET Çekirdek Modülü' nün arkasında [sürücü](xref:host-and-deploy/iis/index#out-of-process-hosting-model) lük sınır dışında barındırılırken Varsayılan olarak [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından etkinleştirilir. İleli Üstbilgi Middleware, iletili üstdeğerlere [(örneğin, IP sızdırma)](https://www.iplocation.net/ip-spoofing)güven endişeleri nedeniyle ASP.NET Çekirdek Modülüne özgü sınırlı bir yapılandırmaile ilk olarak ara yazılım boru hattında çalışacak şekilde etkinleştirilir. Ara yazılım, üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi ileri ye doğru yapılandırılır ve tek bir localhost proxy ile sınırlıdır. Ek yapılandırma gerekiyorsa, [İleriye Dönük Üstbilgi Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Diğer proxy sunucusu ve yük dengeleyici senaryoları

[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma yaparken [IIS Tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) kullanmanın dışında, Iletili Üstbilgi Middleware varsayılan olarak etkinleştirilmez. İleşli Üstbilgi Ara ware, bir uygulamanın <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>iletilir üstbilgisini ' ile işlemesi için etkinleştirilmelidir. Middleware'e hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse ara yazılımı etkinleştirdikten sonra, varsayılan [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) [forwardedheaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Aracı, 'deki <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` üstbilgi ve `X-Forwarded-Proto` üstileri `Startup.ConfigureServices`iletmek için yapılandırın. Diğer <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> ara `Startup.Configure` yazılımları çağırmadan önce yöntemi çağırın:

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
> Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> veya doğrudan `Startup.ConfigureServices` uzantısı yöntemi nde <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>belirtilmişise , ileri varsayılan üstbilgi [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)vardır. Özellik <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> ileri ye doğru üstbilgi ile yapılandırılmalıdır.

## <a name="nginx-configuration"></a>Nginx yapılandırması

Üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi <xref:host-and-deploy/linux-nginx#configure-nginx>ileri almak için bkz. Daha fazla bilgi için [NGINX: İleride üstbilgiyi kullanma.](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)

## <a name="apache-configuration"></a>Apache yapılandırması

`X-Forwarded-For`otomatik olarak eklenir [(bkz. Apache Modülü mod_proxy: Proxy İstek Üstbilgilerini Ters](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)Çevir ). Üstbilginin nasıl iletilen `X-Forwarded-Proto` <xref:host-and-deploy/linux-apache#configure-apache>hakkında bilgi için bkz.

## <a name="forwarded-headers-middleware-options"></a>İleşli Üstbilgi Ara ware seçenekleri

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen Üstbilgi Middleware davranışını kontrol edin. Aşağıdaki örnekte varsayılan değerleri değiştirir:

* İlerlenen üstbilgideki giriş sayısını `2`' la sınırlandırın.
* `127.0.10.1`Bilinen bir proxy adresi ekleyin.
* İdeyiş üstbilgi adını `X-Forwarded-For` varsayılandan ' a `X-Forwarded-For-My-Custom-Header-Name`çevirin.

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
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ana bilgisayarları `X-Forwarded-Host` üstbilgi yle sağlanan değerlerle sınırlandırıyor.<ul><li>Değerler, ordinal-ignore-case kullanılarak karşılaştırılır.</li><li>Bağlantı noktası numaraları hariç tutulmalıdır.</li><li>Liste boşsa, tüm ana bilgisayarlara izin verilir.</li><li>Üst düzey joker `*` karakter, boş olmayan tüm ana bilgisayarları sağlar.</li><li>Alt etki alanı joker kartlarına izin verilir, ancak kök etki alanıyla eşleşmez. Örneğin, `*.contoso.com` alt etki `foo.contoso.com` alanı eşleşir, `contoso.com`ancak kök etki alanı .</li><li>Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [Punycode'a](https://tools.ietf.org/html/rfc3492) dönüştürülür.</li><li>[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı parantezler içermelidir ve [geleneksel](https://tools.ietf.org/html/rfc4291#section-2.2) formda `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`olmalıdır (örneğin, ). IPv6 adresleri, farklı biçimler arasındaki mantıksal eşitliği denetlemek için özel olarak belirtilmemiştir ve hiçbir kanonializasyon gerçekleştirilmez.</li><li>İzin verilen ana bilgisayarların kısıtlanmasına izin verilmese, saldırgan hizmet tarafından oluşturulan bağlantıları sızdırmasına neden olabilir.</li></ul>Varsayılan değer boştur. `IList<string>` |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın. Bu seçenek, proxy/forwarder `X-Forwarded-For` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Hangi iletmelerin işlenmesi gerektiğini belirler. Geçerli alanların listesi için [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) bölümüne bakın. Bu özelliğe atanan `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tipik değerler.<br><br>Varsayılan değer [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)olduğunu. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın. Bu seçenek, proxy/forwarder `X-Forwarded-Host` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın. Bu seçenek, proxy/forwarder `X-Forwarded-Proto` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.<br><br>Varsayılan değer: `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | İşlenen üstbilgideki giriş sayısını sınırlar. Sınırı devre dışı bırakacak şekilde ayarlayın, ancak `KnownProxies` bu `KnownNetworks` yalnızca yapılandırıldıysa veya yapılandırılırsa yapılmalıdır. `null` `null` Değer olmayan bir ayar, ağdaki yan kanallardan gelen yanlış yapılandırılmış yardımcılara ve kötü amaçlı isteklere karşı korunmak için bir önlemdir (ancak garanti değildir).<br><br>İlli Üstbilgi Middleware, üstleri sağdan sola doğru ters sırada işler. Varsayılan değer (`1`) kullanılırsa, değeri `ForwardLimit` artırılmadığı sürece yalnızca üstbilgiden en doğru değer işlenir.<br><br>Varsayılan değer: `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | İleriye dönük üstbilgileri kabul etmek için bilinen ağların adres aralıkları. Sınıfsız Etki Alanı Yönlendirme (CIDR) gösterimini kullanarak IP aralıkları sağlayın.<br><br>Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir). Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin. Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.<br><br>Varsayılan değer, `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> '' için `IPAddress.Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | İleriye dönük üstbilgi kabul etmek için bilinen yakınlık adresleri. Tam `KnownProxies` IP adresi eşleşmelerini belirtmek için kullanın.<br><br>Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir). Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin. Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.<br><br>Varsayılan değer, `IList` \< <xref:System.Net.IPAddress> '' için `IPAddress.IPv6Loopback`tek bir giriş içeren bir>. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.<br><br>Varsayılan değer: `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.<br><br>Varsayılan değer: `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.<br><br>Varsayılan değer: `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Üstbilgi değerlerinin sayısının, işlenen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitolmasını zorunlu kılmasını.<br><br>Core 1.x ASP.NET varsayılan `true`. Core 2.0 veya sonraki ASP.NET `false`varsayılan. |

## <a name="scenarios-and-use-cases"></a>Senaryolar ve kullanım örnekleri

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>İleiletiüstleri eklemek mümkün olmadığında ve tüm isteklerin güvenli olduğu

Bazı durumlarda, uygulamaya iledilen üstbilgi eklemek mümkün olmayabilir. Proxy tüm genel dış isteklerin HTTPS olduğunu zorluyorsa, şema `Startup.Configure` herhangi bir ara yazılım türü kullanmadan önce el ile ayarlanabilir:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Bu kod, bir geliştirme veya evreleme ortamında bir ortam değişkeni veya başka bir yapılandırma ayarı ile devre dışı alınabilir.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>İstek yolunu değiştiren yol tabanı ve yakınlıklarla başa çık

Bazı yakınlıklar yolu sağlam bir şekilde geçer, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama tabanı yolu ile. [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware [httprequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine uygulama temel yolu içine yol böler.

Proxy `/foo` `/foo/api/1`yolu olarak geçirilen bir proxy yolu için uygulama `Request.PathBase` `/foo` temel `Request.Path` `/api/1` yolu ise, ara yazılım aşağıdaki komutla ve ayarlar:

```csharp
app.UsePathBase("/foo");
```

Orta yazılım ters olarak yeniden çağrıldığında özgün yol ve yol tabanı yeniden uygulanır. Ara yazılım siparişi işleme hakkında <xref:fundamentals/middleware/index>daha fazla bilgi için bkz.

Proxy yolu kırparsa (örneğin, `/foo/api/1` `/api/1`iletilmesi), isteğin [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yönlendirmeleri ve bağlantıları düzeltin:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Proxy yol verileri ekliyorsa, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> özelliği kullanarak ve atayarak yeniden yönlendirmeleri ve bağlantıları düzeltmek için yolun bir kısmını atın:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Farklı üstbilgi adları kullanan bir proxy için yapılandırma

Proxy `X-Forwarded-For` adlandırılmış üstbilgileri kullanmıyorsa `X-Forwarded-Proto` ve proxy adresi/bağlantı noktası ve kaynak <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> düzeni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> bilgilerini iletmek için, proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ve seçenekleri ayarlayın:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>IPv6 adresi olarak temsil edilen bir IPv4 adresi için yapılandırma

Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1` IPv6 olarak veya `::ffff:a00:1`olarak temsil edilir). Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*). [HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.

Aşağıdaki örnekte, iletilen üstbilgileri sağlayan bir ağ `KnownNetworks` adresi IPv6 biçiminde listeye eklenir.

IPv4 adresi:`10.11.12.1/8`

Dönüştürülmüş IPv6 adresi:`::ffff:10.11.12.1`  
Dönüştürülmüş önek uzunluğu: 104

Adresi hexadecimal formatta da sağlayabilirsiniz (IPv6'da`10.11.12.1` temsil `::ffff:0a0b:0c01`edilir). Bir IPv4 adresini IPv6'ya dönüştürürken, ek`8` `::ffff:` IPv6 önekini (8 + 96 = 104) hesaba katmak için CIDR Önek Uzunluğu'na (örnekte) 96 ekleyin. 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Linux ve IIS olmayan ters vekiller için şemayı iletin

Bir Azure <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Linux Uygulama Hizmetine, Azure Linux sanal makinesine (VM) veya IIS dışındaki diğer ters proxy'nin arkasına dağıtılırsa, bir siteyi arayan ve sonsuz bir döngüye sokan uygulamalar. TLS ters proxy tarafından sonlandırılır ve Kestrel doğru istek şeması ndan haberdar yapılmaz. OAuth ve OIDC de yanlış yönlendirmeler oluşturdukları için bu yapılandırmada başarısız olurlar. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS'nin arkasında çalışırken İleriye Dönük Üstbilgi Middleware ekler ve yapılandırır, ancak Linux için eşleşen bir otomatik yapılandırma yoktur (Apache veya Nginx entegrasyonu).

Düzeni IIS olmayan senaryolarda proxy'den iletmek için, İleriye Dönük Üstbilgi Ara Ware ekleyin ve yapılandırın. In `Startup.ConfigureServices`, aşağıdaki kodu kullanın:

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

Üstbilgi beklendiği gibi iletilmediğinde, [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin. Günlükler sorunu gidermek için yeterli bilgi sağlamazsa, sunucu tarafından alınan istek üstbilgilerini kaydedin. İstek üstbilgilerini bir uygulama yanıtına yazmak veya üstbilgigünlerini günlüğe kaydetmek için satır içi ara yazılım kullanın. 

Üstbilgileri uygulamanın yanıtına yazmak için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> aşağıdaki terminal satır ara sını aşağıdaki `Startup.Configure`çağrıdan hemen sonra yerleştirin:

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

Yanıt gövdesi yerine günlüklere yazabilirsiniz. Günlüklere yazma, hata ayıklama sırasında sitenin normal çalışmasını sağlar.

Yanıt gövdesiyerine günlükyazmak için:

* Başlangıç'ta `Startup` create [günlüklerinde](xref:fundamentals/logging/index#create-logs-in-startup)açıklandığı gibi sınıfa enjekte edin. `ILogger<Startup>`
* Aşağıdaki satır lı ara yazılımı aramadan <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`hemen sonra yerleştirin.

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

İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler ' `X-Original-{For|Proto|Host}`e taşınır. Belirli bir üstbilgide birden çok değer varsa, İlliyeli Üstbilgi Orta Ware üstileri sağdan sola doğru ters sırada işler. Varsayılan `ForwardLimit` `1` değer (bir), bu nedenle değeri `ForwardLimit` artırılmadığı sürece üstbilgiden yalnızca en doğru değer işlenir.

İdegöreli üstbilgi işlenmeden önce `KnownProxies` `KnownNetworks` isteğin orijinal uzak IP'si veya listelerdeki bir girişle eşleşmelidir. Bu, ileticileri güvenilmeyen vekillerden kabul etmeyerak başlık sızdırmayı sınırlar. Bilinmeyen bir proxy algılandığında, günlüğe kaydetme proxy'nin adresini gösterir:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Önceki örnekte, 10.0.0.100 bir proxy sunucusudur. Sunucu güvenilir bir proxy ise, sunucunun IP adresini `KnownProxies` ekleyin (veya güvenilir `KnownNetworks`bir `Startup.ConfigureServices`ağ ekleyin) içinde. Daha fazla bilgi [için, İlliyeli Üstbilgi Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Yalnızca güvenilen yakınlık ve ağların üstbilgi ilerisine girmesine izin verin. Aksi takdirde, [IP sızdırma saldırıları mümkündür.](https://www.iplocation.net/ip-spoofing)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787: ASP.NET Temel Ayrıcalık Yüksekliği Güvenlik Açığı](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
