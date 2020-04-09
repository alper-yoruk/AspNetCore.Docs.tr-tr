---
title: URL ASP.NET Core'da Middleware'i Yeniden Yazma
author: rick-anderson
description: ASP.NET Core uygulamalarında URL Yeniden Yazma Middleware ile URL yeniden yazma ve yeniden yönlendirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: 7d63cf381f1d8a19ed4fb789348e36f94304ad63
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666469"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>URL ASP.NET Core'da Middleware'i Yeniden Yazma

Yazar: [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

Bu belge, ASP.NET Core uygulamalarında URL Yeniden Yazma Middleware'in nasıl kullanılacağına ilişkin yönergelerle BIRLIKTE URL yeniden yazmayı tanır.

URL yeniden yazma, istek URL'lerini önceden tanımlanmış bir veya daha fazla kurala göre değiştirme eylemidir. URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmamalarını sağlayacak şekilde kaynak konumları ve adresleri arasında bir soyutlama oluşturur. URL yeniden yazma çeşitli senaryolarda değerlidir:

* Sunucu kaynaklarını geçici veya kalıcı olarak taşıyın veya değiştirin ve bu kaynaklar için kararlı yer bulabilenleri koruyun.
* İstek işlemeyi farklı uygulamalara veya bir uygulamanın alanlarında bölme.
* Gelen isteklerde URL bölümlerini kaldırın, ekleyin veya yeniden düzenleyin.
* Arama Motoru Optimizasyonu (SEO) için genel URL'leri optimize edin.
* Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmelerine yardımcı olmak için dost canlı URL'lerinin kullanılmasına izin ver.
* Güvenli uç noktalarına güvenli olmayan istekleri yeniden yönlendirin.
* Harici bir sitenin, varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı hotlinking'i önleyin.

> [!NOTE]
> URL yeniden yazma, bir uygulamanın performansını azaltabilir. Mümkün olduğu durumlarda, kuralların sayısını ve karmaşıklığını sınırlayın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>URL yönlendirme ve URL yeniden yazma

*URL yönlendirme* ve URL yeniden *yazma* arasındaki ifade farkı ince, ancak istemcilere kaynak sağlamak için önemli etkileri vardır. ASP.NET Core URL Rewriting Middleware her ikisi için de ihtiyacı nı karşılama yeteneğine sahiptir.

*URL* yönlendirmesi, istemcinin bir kaynağa istemcinin başlangıçta talep edilenden farklı bir adreste erişmetalimatı verdiği istemci tarafı çalışmasını içerir. Bu sunucuya bir gidiş-dönüş gerektirir. İstemci ye döndürülen yönlendirme URL'si, istemci kaynak için yeni bir istekte bulununca tarayıcının adres çubuğunda görünür.

Bu duruma *yönlendirilirse,* sunucu istemcinin kaynağı yeniden `/different-resource` yönlendirmenin geçici veya kalıcı olduğunu belirten bir durum koduyla elde etmesi gerektiği yanıtını verir. `/resource` `/different-resource`

![Bir WebAPI hizmet bitiş noktası, sunucudaki sürüm 1'den (v1) sürüm 2'ye (v2) geçici olarak değiştirildi. İstemci, sürüm 1 yolu /v1/api adresinden hizmete bir istekte bulunmaz. Sunucu, sürüm 2 /v2/api adresindeki hizmet için yeni, geçici bir yol ile birlikte 302 (Bulundu) yanıtı gönderir. İstemci, yeniden yönlendirme URL'sinde hizmete ikinci bir istekte bulunr. Sunucu 200 (Tamam) durum koduyla yanıt verir.](url-rewriting/_static/url_redirect.png)

İstekleri farklı bir URL'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yönlendirmenin kalıcı mı yoksa geçici mi olduğunu belirtin:

* *301 - Taşınmış Kalıcı* durum kodu, kaynağın yeni ve kalıcı bir URL'si olduğu ve istemciye kaynak için gelecekteki tüm isteklerin yeni URL'yi kullanması gerektiğini söylemek istediğiniz durumlarda kullanılır. *İstemci, 301 durum kodu aldığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*

* *302 - Bulunan* durum kodu, yeniden yönlendirmenin geçici olduğu veya genel olarak değiştirildiği durumlarda kullanılır. 302 durum kodu, istemciye URL'yi depolamamasını ve gelecekte kullanmamasını gösterir.

Durum kodları hakkında daha fazla bilgi için [Bkz. RFC 2616: Durum Kodu Tanımları.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

*URL yeniden yazma,* istemcinin istediğinden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı işlemidir. URL'yi yeniden yazmak için sunucuya gidiş-dönüş seyahat gerekmez. Yeniden yazılan URL istemciye döndürülür ve tarayıcının adres çubuğunda görünmez.

Yeniden `/resource` `/different-resource` *yazılırsa,* sunucu *kaynağı 'nda* `/different-resource`dahili olarak getirir ve döndürür.

İstemci kaynağı yeniden yazılmış URL'den alabilse de, istemci, isteği nde bulunduğunda ve yanıtı aldığında kaynağın yeniden yazılmış URL'de bulunduğu konusunda bilgilendirilmemektedir.

![WebAPI hizmet bitiş noktası sunucudaki sürüm 1 'den (v1) sürüm 2 'ye (v2) değiştirildi. İstemci, sürüm 1 yolu /v1/api adresinden hizmete bir istekte bulunmaz. İstek URL'si, sürüm 2 yolu /v2/api adresindeki hizmete erişmek için yeniden yazılır. Hizmet istemciye 200 (Tamam) durum koduyla yanıt verir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL yeniden yazma örnek uygulaması

[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)ile URL Rewriting Middleware özelliklerini keşfedebilirsiniz. Uygulama yeniden yönlendirme ve yeniden yazma kuralları nı uygular ve çeşitli senaryolar için yeniden yönlendirilen veya yeniden yazılan URL'yi gösterir.

## <a name="when-to-use-url-rewriting-middleware"></a>URL Rewriting Middleware ne zaman kullanılır

Aşağıdaki yaklaşımları kullanamadığınızda URL Yeniden Yazma Middleware'i kullanın:

* [Windows Server'da IIS ile URL Yeniden Yazma Modülü](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache Server'da Apache mod_rewrite modülü](https://httpd.apache.org/docs/2.4/rewrite/)
* [Nginx'te URL yeniden yazma](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Ayrıca, uygulama [HTTP.sys sunucusunda](xref:fundamentals/servers/httpsys) (eski adıyla WebListener olarak adlandırılır) barındırıldığında ara yazılımı kullanın.

IIS, Apache ve Nginx'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:

* Ara yazılım bu modüllerin tüm özelliklerini desteklemez.

  Sunucu modüllerinin bazı özellikleri, IIS Yeniden Yazma modülünün `IsFile` kısıtlamaları `IsDirectory` ve kısıtlamaları gibi ASP.NET Core projeleri ile çalışmaz. Bu senaryolarda, bunun yerine ara yazılımı kullanın.
* Ara yazılımın performansı muhtemelen modüllerin performansıyla eşleşmiyor.

  Kıyaslama, hangi yaklaşımın performansı en çok düşürdüğünü veya azalan performansın ihmal edilebilir olup olmadığını kesin olarak bilmenin tek yoludur.

## <a name="package"></a>Paket

URL Rewriting Middleware, ASP.NET Core uygulamalarına dolaylı olarak dahil edilen [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketi tarafından sağlanmaktadır.

## <a name="extension-and-options"></a>Uzantı ve seçenekler

Yeniden yazma kurallarınızın her biri için uzantı yöntemleriiçeren [Yeniden Yazma Seçenekleri](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir örneğini oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları nı belirleyin. İşlenmelerini istediğiniz sırayla birden çok kural zincirleyin. İstek `RewriteOptions` ardışık hattına eklenerek URL Yeniden Yazma Middleware'e <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>aktarılır:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Www olmayanları www'e yönlendirme

Üç seçenek, uygulamanın olmayan`www` istekleri `www`aşağıdakilere yönlendirmesine izin verir:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; İstek yoksa, isteği kalıcı `www` olarak alt etki alanına`www`yönlendirin. [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum koduyla yeniden yönlendirir.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Gelen istek yoksa, isteği `www` alt etki alanına`www`yönlendirin. [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum koduyla yeniden yönlendirir. Aşırı yükleme, yanıtın durum kodunu sağlamanıza izin verir. Durum kodu ataması <xref:Microsoft.AspNetCore.Http.StatusCodes> için sınıfın alanını kullanın.

### <a name="url-redirect"></a>URL yeniden yönlendirme

İstekleri yönlendirmek için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> İlk parametre, gelen URL'nin yolunda eşleştirme için regex'inizi içerir. İkinci parametre değiştirme dizesidir. Üçüncü parametre, varsa durum kodunu belirtir. Durum kodunu belirtmezseniz, durum kodu varsayılan *olarak 302 - Found*, kaynağın geçici olarak taşındığını veya değiştirilmediğini gösterir.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

Geliştirici araçları etkin leştirilmiş bir tarayıcıda, yol `/redirect-rule/1234/5678`ile örnek uygulama için bir istekte bulunun. Regex, istek yolu `redirect-rule/(.*)`üzerinde eşleşir ve yol `/redirected/1234/5678`' ile değiştirilir. Yönlendirme URL'si *302 - Found* durum koduyla istemciye geri gönderilir. Tarayıcı, tarayıcının adres çubuğunda görünen yönlendirme URL'sinde yeni bir istekte bulunr. Örnek uygulamadaki kural yönlendirme URL'sinde eşleşmediğinden:

* İkinci istek uygulamadan *200 - Tamam* yanıtı alır.
* Yanıtın gövdesi yönlendirme URL'sini gösterir.

URL *yönlendirildiğinde*sunucuya gidiş-dönüş yapılır.

> [!WARNING]
> Yönlendirme kuralları oluştururken dikkatli olun. Yönlendirme kuralları, yönlendirme sonrası da dahil olmak üzere uygulamaya yapılan her istekte değerlendirilir. Yanlışlıkla *sonsuz yönlendirmeler*bir döngü oluşturmak kolaydır.

Orijinal İstek:`/redirect-rule/1234/5678`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

İfadenin parantez içinde bulunan bölümüne *yakalama grubu*denir. İfadenin nokta`.`( ) *herhangi bir karakterle eşleşmesi*anlamına gelir. Yıldız işareti (`*`) *önceki karakter sıfır veya daha fazla kez eşleşir.* Bu nedenle, URL'nin son iki `1234/5678`yol kesimi, `(.*)`yakalama grubu tarafından yakalanır. İstek URL'sinde `redirect-rule/` sağladığınız herhangi bir değer bu tek yakalama grubu tarafından yakalanır.

Yedek dizede, yakalanan gruplar dolar işareti ile dize içine enjekte edilir (`$`) yakalama sıra numarası takip. İlk yakalama grubu değeri `$1`, ikinci `$2`ile elde edilir ve regex yakalama grupları için sırayla devam eder. Örnek uygulamasında yönlendirme kuralı regex'te yakalanan tek bir grup var. `$1` Kural uygulandığında, URL olur. `/redirected/1234/5678`

### <a name="url-redirect-to-a-secure-endpoint"></a>URL güvenli bir bitiş noktasına yönlendirilir

HTTPS <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> protokolünü kullanarak HTTP isteklerini aynı ana bilgisayara ve yola yönlendirmek için kullanın. Durum kodu sağlanmıyorsa, ara yazılım varsayılan *olarak 302 - Bulundu.* Bağlantı noktası sağlanmıyorsa:

* Ara yazılım varsayılan `null`olarak .
* Şema (HTTPS protokolü) olarak `https` değişir ve istemci bağlantı noktası 443'teki kaynağa erişer.

Aşağıdaki örnek, durum *kodunun 301 - Moved Permanent'e* nasıl ayarlanır ve bağlantı noktasını 5001 olarak nasıl değiştireceğini gösterir.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Güvenli <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> olmayan istekleri aynı ana bilgisayara ve bağlantı noktası 443'teki güvenli HTTPS protokolüyle yola yönlendirmek için kullanın. Ara yazılım durum kodunu *301*olarak ayarlar - Kalıcı olarak taşınır.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Ek yeniden yönlendirme kuralları gerekmeden güvenli bir bitiş noktasına yönlendirirken, HTTPS Yönlendirme Middleware'i kullanmanızı öneririz. Daha fazla bilgi için [HTTPS'yi Uygula](xref:security/enforcing-ssl#require-https) konusuna bakın.

Örnek uygulama nasıl kullanılacağını veya `AddRedirectToHttps` `AddRedirectToHttpsPermanent`nasıl kullanılacağını gösterebilme yeteneğine sahiptir. Uzantı yöntemini `RewriteOptions`. Herhangi bir URL'den uygulamaya güvenli olmayan bir istekte bulunun. Tarayıcı güvenlik uyarısını, kendi imzaladığı sertifikanın güvenilen olmadığı konusunda görevden alın veya sertifikaya güvenmek için bir özel durum oluşturun.

Orijinal İstek `AddRedirectToHttps(301, 5001)`kullanarak:`http://localhost:5000/secure`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

Orijinal İstek `AddRedirectToHttpsPermanent`kullanarak:`http://localhost:5000/secure`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL yeniden yazma

URL'leri yeniden yazmak için bir kural oluşturmak için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> İlk parametre, gelen URL yolunda eşleştirme için regex içerir. İkinci parametre değiştirme dizesidir. Üçüncü parametre, `skipRemainingRules: {true|false}`geçerli kural uygulanırsa ek yeniden yazma kuralları atlamak olsun veya olmasın middleware gösterir.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Orijinal İstek:`/rewrite-rule/1234/5678`

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

İfadenin başındaki karat (`^`) eşleştirmenin URL yolunun başında başladığı anlamına gelir.

Yönlendirme kuralı ile önceki örnekte, `redirect-rule/(.*)`regex başında hiçbir`^`karat () var. Bu nedenle, herhangi bir `redirect-rule/` karakter başarılı bir eşleşme için yolda önce olabilir.

| Yol                               | Eşleştirme |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Evet   |
| `/my-cool-redirect-rule/1234/5678` | Evet   |
| `/anotherredirect-rule/1234/5678`  | Evet   |

Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)`yalnızca ' ile `rewrite-rule/`başlarlarsa yollar eşleşir. Aşağıdaki tabloda, eşleştirme farkı dikkat edin.

| Yol                              | Eşleştirme |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Evet   |
| `/my-cool-rewrite-rule/1234/5678` | Hayır    |
| `/anotherrewrite-rule/1234/5678`  | Hayır    |

İfadenin `^rewrite-rule/` bir bölümünü takiben, iki `(\d+)/(\d+)`yakalama grubu vardır. Bir `\d` basamak *(sayı) eşleşmesi*anlamına gelen. Artı işareti`+`( ) *önceki karakterin bir veya daha fazla eşleşmesi*anlamına gelir. Bu nedenle, URL bir sayı ve ardından başka bir sayı takip eden bir sayı içermelidir. Bu yakalama grupları yeniden yazılan URL'ye `$2`. `$1` Yeniden yazma kuralı değiştirme dizesi, yakalanan grupları sorgu dizesine yerleştirir. İstenen `/rewrite-rule/1234/5678` yol, kaynağı n ' `/rewritten?var1=1234&var2=5678`den elde etmek için yeniden yazılır. Özgün istekte bir sorgu dizesi varsa, URL yeniden yazıldığında korunur.

Kaynağı elde etmek için sunucuya gidiş dönüş yok. Kaynak varsa, *200 - Tamam* durum koduyla getirilir ve istemciye döndürülür. İstemci yönlendirilmediği için tarayıcının adres çubuğundaki URL değişmez. İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamamaktadır.

> [!NOTE]
> Eşleşen `skipRemainingRules: true` kurallar hesaplama olarak pahalı olduğundan ve uygulama yanıt süresini artırdığından, mümkün olduğunca kullanın. En hızlı uygulama yanıtı için:
>
> * En sık eşleşen kuraldan en sık eşleşen kurala yeniden yazma kurallarını sırala.
> * Eşleşme oluştuğunda ve ek kural işleme gerektirildiğinde kalan kuralların işlenmesini atlayın.

### <a name="apache-mod_rewrite"></a>Apaçi mod_rewrite

Apache mod_rewrite kurallarını <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>. Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun. Daha fazla bilgi ve mod_rewrite kuralları örnekleri için, [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)bakın.

<xref:System.IO.StreamReader> A, *ApacheModRewrite.txt* kuralları dosyasındaki kuralları okumak için kullanılır:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Örnek uygulama istekleri ' `/apache-mod-rules-redirect/(.\*)` den `/redirected?id=$1`' e yönlendirir. Yanıt durum kodu *302 - Bulundu.*

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

Orijinal İstek:`/apache-mod-rules-redirect/1234`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* Remote_addr
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>IIS URL Yeniden Yazma Modülü kuralları

IIS URL Yeniden Yazma Modülü için geçerli olan aynı <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>kural kümesini kullanmak için . Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun. Windows Server IIS'de çalışırken ara yazılımı uygulamanın *web.config* dosyasını kullanmaya yönlendirmeyin. IIS ile bu kurallar, IIS Yeniden Yazma modülüyle çakışmaları önlemek için uygulamanın *web.config* dosyasının dışında saklanmalıdır. IIS URL Yeniden Yazma Modülü kuralları hakkında daha fazla bilgi [ve](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) örnekler için [bkz.](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)

<xref:System.IO.StreamReader> A, *IISUrlRewrite.xml* kuralları dosyasındaki kuralları okumak için kullanılır:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Örnek uygulama isteklerini `/iis-rules-rewrite/(.*)` 'den ' e `/rewritten?id=$1`yeniden yazar Yanıt istemciye *200 - Tamam* durum koduyla gönderilir.

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

Orijinal İstek:`/iis-rules-rewrite/1234`

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

Uygulamanızı istenmeyen şekillerde etkileyecek şekilde yapılandırılan sunucu düzeyinde kurallara sahip etkin bir IIS Yeniden Yazma Modülünüz varsa, bir uygulama için IIS Yeniden Yazma Modül'ünden devre dışı kullanabilirsiniz. Daha fazla bilgi için Bkz. [IIS modüllerini devre dışı bırakma.](xref:host-and-deploy/iis/modules#disabling-iis-modules)

#### <a name="unsupported-features"></a>Desteklenmeyen özellikler

Core 2.x ile ASP.NET yayımlanan ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü özelliklerini desteklemez:

* Giden Kuralları
* Özel Sunucu Değişkenleri
* Joker karakterler
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Desteklenen sunucu değişkenleri

Ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü sunucu değişkenlerini destekler:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* Remote_addr
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Ayrıca bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>üzerinden elde edebilirsiniz . Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir. Yeniden yazma kuralları dosyalarınızın sağladığınız yolda sunucuya dağıtıldığınızdan emin olun.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Yöntem tabanlı kural

Bir <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> yöntemde kendi kural mantığınızı uygulamak için kullanın. `Add`<xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, yönteminizde kullanıma hazır <xref:Microsoft.AspNetCore.Http.HttpContext> hale getirir. [Yeniden Yazma Bağlamı.Sonuç,](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) ek boru hattı işlemenin nasıl işleneceğini belirler. Değeri aşağıdaki tabloda <xref:Microsoft.AspNetCore.Rewrite.RuleResult> açıklanan alanlardan birine ayarlayın.

| `RewriteContext.Result`              | Eylem                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(varsayılan) | Kuralları uygulamaya devam edin.                                         |
| `RuleResult.EndResponse`             | Kuralları uygulamayı bırakın ve yanıtı gönderin.                       |
| `RuleResult.SkipRemainingRules`      | Kuralları uygulamayı bırakın ve bağlamı bir sonraki ara yazılıma gönderin. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Örnek *uygulama.xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntem gösterir. Bir `/file.xml`istek için yapılırsa, istek `/xmlfiles/file.xml`. Durum kodu *301*olarak ayarlanır - Kalıcı olarak taşınır. Tarayıcı */xmlfiles/file.xml*için yeni bir istekte bulunursa, Statik Dosya Middleware *wwwroot/xmlfiles* klasöründen dosyayı istemciye sunar. Yeniden yönlendirme için yanıtın durum kodunu açıkça ayarlayın. Aksi takdirde, *200 - Tamam* durum kodu döndürülür ve yönlendirme istemcide oluşmaz.

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Bu yaklaşım istekleri yeniden yazabilir. Örnek *uygulama, wwwroot* klasöründen *file.txt* metin dosyasına hizmet vermek için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir. Statik Dosya Middleware güncelleştirilmiş istek yolunu temel alarak dosyaya hizmet vermektedir:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>IRule tabanlı kural

Arabirimi uygulayan bir sınıfta kural mantığını kullanmak için <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> <xref:Microsoft.AspNetCore.Rewrite.IRule> kullanın. `IRule`yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar. Uygulama sınıfınız, <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> yöntem için parametreleri geçirmenize olanak tanıyan bir oluşturucu içerebilir.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Örnek uygulamadaki parametrelerin değerleri için `extension` ve `newPath` çeşitli koşulları karşılamak için kontrol edilir. Bir `extension` değer içermelidir ve değeri *.png*, *.jpg*veya *.gif*olmalıdır. Geçerli `newPath` değilse, bir <xref:System.ArgumentException> atılır. *image.png*için bir istek yapılırsa, `/png-images/image.png`istek . *image.jpg*için bir istek yapılırsa, `/jpg-images/image.jpg`istek . Durum kodu *301*olarak ayarlanır - Kalıcı `context.Result` olarak taşınır ve işleme kurallarını durdurmak ve yanıtı göndermek için ayarlanır.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Orijinal İstek:`/image.png`

![Geliştirici Araçları ile tarayıcı penceresi image.png için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_png_requests.png)

Orijinal İstek:`/image.jpg`

![Geliştirici Araçları ile tarayıcı penceresi image.jpg için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Regex örnekleri

| Hedef | Regex String &<br>Eşle' nin Örneği | Yedek Dize &<br>Çıktı Örneği |
| ---- | ------------------------------- | -------------------------------------- |
| Yolu querystring'e yeniden yazma | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Şerit izleme eğik çizgi | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Sondaki eğik çizgiyi zorla | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Belirli istekleri yeniden yazmaktan kaçının | `^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`<br>Evet:`/resource.htm`<br>№:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| URL segmentlerini yeniden düzenleme | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| URL kesimini değiştirme | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu belge, ASP.NET Core uygulamalarında URL Yeniden Yazma Middleware'in nasıl kullanılacağına ilişkin yönergelerle BIRLIKTE URL yeniden yazmayı tanır.

URL yeniden yazma, istek URL'lerini önceden tanımlanmış bir veya daha fazla kurala göre değiştirme eylemidir. URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmamalarını sağlayacak şekilde kaynak konumları ve adresleri arasında bir soyutlama oluşturur. URL yeniden yazma çeşitli senaryolarda değerlidir:

* Sunucu kaynaklarını geçici veya kalıcı olarak taşıyın veya değiştirin ve bu kaynaklar için kararlı yer bulabilenleri koruyun.
* İstek işlemeyi farklı uygulamalara veya bir uygulamanın alanlarında bölme.
* Gelen isteklerde URL bölümlerini kaldırın, ekleyin veya yeniden düzenleyin.
* Arama Motoru Optimizasyonu (SEO) için genel URL'leri optimize edin.
* Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmelerine yardımcı olmak için dost canlı URL'lerinin kullanılmasına izin ver.
* Güvenli uç noktalarına güvenli olmayan istekleri yeniden yönlendirin.
* Harici bir sitenin, varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı hotlinking'i önleyin.

> [!NOTE]
> URL yeniden yazma, bir uygulamanın performansını azaltabilir. Mümkün olduğu durumlarda, kuralların sayısını ve karmaşıklığını sınırlayın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>URL yönlendirme ve URL yeniden yazma

*URL yönlendirme* ve URL yeniden *yazma* arasındaki ifade farkı ince, ancak istemcilere kaynak sağlamak için önemli etkileri vardır. ASP.NET Core URL Rewriting Middleware her ikisi için de ihtiyacı nı karşılama yeteneğine sahiptir.

*URL* yönlendirmesi, istemcinin bir kaynağa istemcinin başlangıçta talep edilenden farklı bir adreste erişmetalimatı verdiği istemci tarafı çalışmasını içerir. Bu sunucuya bir gidiş-dönüş gerektirir. İstemci ye döndürülen yönlendirme URL'si, istemci kaynak için yeni bir istekte bulununca tarayıcının adres çubuğunda görünür.

Bu duruma *yönlendirilirse,* sunucu istemcinin kaynağı yeniden `/different-resource` yönlendirmenin geçici veya kalıcı olduğunu belirten bir durum koduyla elde etmesi gerektiği yanıtını verir. `/resource` `/different-resource`

![Bir WebAPI hizmet bitiş noktası, sunucudaki sürüm 1'den (v1) sürüm 2'ye (v2) geçici olarak değiştirildi. İstemci, sürüm 1 yolu /v1/api adresinden hizmete bir istekte bulunmaz. Sunucu, sürüm 2 /v2/api adresindeki hizmet için yeni, geçici bir yol ile birlikte 302 (Bulundu) yanıtı gönderir. İstemci, yeniden yönlendirme URL'sinde hizmete ikinci bir istekte bulunr. Sunucu 200 (Tamam) durum koduyla yanıt verir.](url-rewriting/_static/url_redirect.png)

İstekleri farklı bir URL'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yönlendirmenin kalıcı mı yoksa geçici mi olduğunu belirtin:

* *301 - Taşınmış Kalıcı* durum kodu, kaynağın yeni ve kalıcı bir URL'si olduğu ve istemciye kaynak için gelecekteki tüm isteklerin yeni URL'yi kullanması gerektiğini söylemek istediğiniz durumlarda kullanılır. *İstemci, 301 durum kodu aldığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*

* *302 - Bulunan* durum kodu, yeniden yönlendirmenin geçici olduğu veya genel olarak değiştirildiği durumlarda kullanılır. 302 durum kodu, istemciye URL'yi depolamamasını ve gelecekte kullanmamasını gösterir.

Durum kodları hakkında daha fazla bilgi için [Bkz. RFC 2616: Durum Kodu Tanımları.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

*URL yeniden yazma,* istemcinin istediğinden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı işlemidir. URL'yi yeniden yazmak için sunucuya gidiş-dönüş seyahat gerekmez. Yeniden yazılan URL istemciye döndürülür ve tarayıcının adres çubuğunda görünmez.

Yeniden `/resource` `/different-resource` *yazılırsa,* sunucu *kaynağı 'nda* `/different-resource`dahili olarak getirir ve döndürür.

İstemci kaynağı yeniden yazılmış URL'den alabilse de, istemci, isteği nde bulunduğunda ve yanıtı aldığında kaynağın yeniden yazılmış URL'de bulunduğu konusunda bilgilendirilmemektedir.

![WebAPI hizmet bitiş noktası sunucudaki sürüm 1 'den (v1) sürüm 2 'ye (v2) değiştirildi. İstemci, sürüm 1 yolu /v1/api adresinden hizmete bir istekte bulunmaz. İstek URL'si, sürüm 2 yolu /v2/api adresindeki hizmete erişmek için yeniden yazılır. Hizmet istemciye 200 (Tamam) durum koduyla yanıt verir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL yeniden yazma örnek uygulaması

[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)ile URL Rewriting Middleware özelliklerini keşfedebilirsiniz. Uygulama yeniden yönlendirme ve yeniden yazma kuralları nı uygular ve çeşitli senaryolar için yeniden yönlendirilen veya yeniden yazılan URL'yi gösterir.

## <a name="when-to-use-url-rewriting-middleware"></a>URL Rewriting Middleware ne zaman kullanılır

Aşağıdaki yaklaşımları kullanamadığınızda URL Yeniden Yazma Middleware'i kullanın:

* [Windows Server'da IIS ile URL Yeniden Yazma Modülü](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache Server'da Apache mod_rewrite modülü](https://httpd.apache.org/docs/2.4/rewrite/)
* [Nginx'te URL yeniden yazma](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Ayrıca, uygulama [HTTP.sys sunucusunda](xref:fundamentals/servers/httpsys) (eski adıyla WebListener olarak adlandırılır) barındırıldığında ara yazılımı kullanın.

IIS, Apache ve Nginx'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:

* Ara yazılım bu modüllerin tüm özelliklerini desteklemez.

  Sunucu modüllerinin bazı özellikleri, IIS Yeniden Yazma modülünün `IsFile` kısıtlamaları `IsDirectory` ve kısıtlamaları gibi ASP.NET Core projeleri ile çalışmaz. Bu senaryolarda, bunun yerine ara yazılımı kullanın.
* Ara yazılımın performansı muhtemelen modüllerin performansıyla eşleşmiyor.

  Kıyaslama, hangi yaklaşımın performansı en çok düşürdüğünü veya azalan performansın ihmal edilebilir olup olmadığını kesin olarak bilmenin tek yoludur.

## <a name="package"></a>Paket

Projenize ara yazılımı eklemek için, [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketini içeren proje dosyasına [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app) bir paket başvurusu ekleyin.

`Microsoft.AspNetCore.App` Meta paketi kullanmadığınızda, `Microsoft.AspNetCore.Rewrite` pakete bir proje başvurusu ekleyin.

## <a name="extension-and-options"></a>Uzantı ve seçenekler

Yeniden yazma kurallarınızın her biri için uzantı yöntemleriiçeren [Yeniden Yazma Seçenekleri](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir örneğini oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları nı belirleyin. İşlenmelerini istediğiniz sırayla birden çok kural zincirleyin. İstek `RewriteOptions` ardışık hattına eklenerek URL Yeniden Yazma Middleware'e <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>aktarılır:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Www olmayanları www'e yönlendirme

Üç seçenek, uygulamanın olmayan`www` istekleri `www`aşağıdakilere yönlendirmesine izin verir:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; İstek yoksa, isteği kalıcı `www` olarak alt etki alanına`www`yönlendirin. [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum koduyla yeniden yönlendirir.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Gelen istek yoksa, isteği `www` alt etki alanına`www`yönlendirin. [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum koduyla yeniden yönlendirir. Aşırı yükleme, yanıtın durum kodunu sağlamanıza izin verir. Durum kodu ataması <xref:Microsoft.AspNetCore.Http.StatusCodes> için sınıfın alanını kullanın.

### <a name="url-redirect"></a>URL yeniden yönlendirme

İstekleri yönlendirmek için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> İlk parametre, gelen URL'nin yolunda eşleştirme için regex'inizi içerir. İkinci parametre değiştirme dizesidir. Üçüncü parametre, varsa durum kodunu belirtir. Durum kodunu belirtmezseniz, durum kodu varsayılan *olarak 302 - Found*, kaynağın geçici olarak taşındığını veya değiştirilmediğini gösterir.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

Geliştirici araçları etkin leştirilmiş bir tarayıcıda, yol `/redirect-rule/1234/5678`ile örnek uygulama için bir istekte bulunun. Regex, istek yolu `redirect-rule/(.*)`üzerinde eşleşir ve yol `/redirected/1234/5678`' ile değiştirilir. Yönlendirme URL'si *302 - Found* durum koduyla istemciye geri gönderilir. Tarayıcı, tarayıcının adres çubuğunda görünen yönlendirme URL'sinde yeni bir istekte bulunr. Örnek uygulamadaki kural yönlendirme URL'sinde eşleşmediğinden:

* İkinci istek uygulamadan *200 - Tamam* yanıtı alır.
* Yanıtın gövdesi yönlendirme URL'sini gösterir.

URL *yönlendirildiğinde*sunucuya gidiş-dönüş yapılır.

> [!WARNING]
> Yönlendirme kuralları oluştururken dikkatli olun. Yönlendirme kuralları, yönlendirme sonrası da dahil olmak üzere uygulamaya yapılan her istekte değerlendirilir. Yanlışlıkla *sonsuz yönlendirmeler*bir döngü oluşturmak kolaydır.

Orijinal İstek:`/redirect-rule/1234/5678`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

İfadenin parantez içinde bulunan bölümüne *yakalama grubu*denir. İfadenin nokta`.`( ) *herhangi bir karakterle eşleşmesi*anlamına gelir. Yıldız işareti (`*`) *önceki karakter sıfır veya daha fazla kez eşleşir.* Bu nedenle, URL'nin son iki `1234/5678`yol kesimi, `(.*)`yakalama grubu tarafından yakalanır. İstek URL'sinde `redirect-rule/` sağladığınız herhangi bir değer bu tek yakalama grubu tarafından yakalanır.

Yedek dizede, yakalanan gruplar dolar işareti ile dize içine enjekte edilir (`$`) yakalama sıra numarası takip. İlk yakalama grubu değeri `$1`, ikinci `$2`ile elde edilir ve regex yakalama grupları için sırayla devam eder. Örnek uygulamasında yönlendirme kuralı regex'te yakalanan tek bir grup var. `$1` Kural uygulandığında, URL olur. `/redirected/1234/5678`

### <a name="url-redirect-to-a-secure-endpoint"></a>URL güvenli bir bitiş noktasına yönlendirilir

HTTPS <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> protokolünü kullanarak HTTP isteklerini aynı ana bilgisayara ve yola yönlendirmek için kullanın. Durum kodu sağlanmıyorsa, ara yazılım varsayılan *olarak 302 - Bulundu.* Bağlantı noktası sağlanmıyorsa:

* Ara yazılım varsayılan `null`olarak .
* Şema (HTTPS protokolü) olarak `https` değişir ve istemci bağlantı noktası 443'teki kaynağa erişer.

Aşağıdaki örnek, durum *kodunun 301 - Moved Permanent'e* nasıl ayarlanır ve bağlantı noktasını 5001 olarak nasıl değiştireceğini gösterir.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Güvenli <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> olmayan istekleri aynı ana bilgisayara ve bağlantı noktası 443'teki güvenli HTTPS protokolüyle yola yönlendirmek için kullanın. Ara yazılım durum kodunu *301*olarak ayarlar - Kalıcı olarak taşınır.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Ek yeniden yönlendirme kuralları gerekmeden güvenli bir bitiş noktasına yönlendirirken, HTTPS Yönlendirme Middleware'i kullanmanızı öneririz. Daha fazla bilgi için [HTTPS'yi Uygula](xref:security/enforcing-ssl#require-https) konusuna bakın.

Örnek uygulama nasıl kullanılacağını veya `AddRedirectToHttps` `AddRedirectToHttpsPermanent`nasıl kullanılacağını gösterebilme yeteneğine sahiptir. Uzantı yöntemini `RewriteOptions`. Herhangi bir URL'den uygulamaya güvenli olmayan bir istekte bulunun. Tarayıcı güvenlik uyarısını, kendi imzaladığı sertifikanın güvenilen olmadığı konusunda görevden alın veya sertifikaya güvenmek için bir özel durum oluşturun.

Orijinal İstek `AddRedirectToHttps(301, 5001)`kullanarak:`http://localhost:5000/secure`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

Orijinal İstek `AddRedirectToHttpsPermanent`kullanarak:`http://localhost:5000/secure`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL yeniden yazma

URL'leri yeniden yazmak için bir kural oluşturmak için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> İlk parametre, gelen URL yolunda eşleştirme için regex içerir. İkinci parametre değiştirme dizesidir. Üçüncü parametre, `skipRemainingRules: {true|false}`geçerli kural uygulanırsa ek yeniden yazma kuralları atlamak olsun veya olmasın middleware gösterir.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Orijinal İstek:`/rewrite-rule/1234/5678`

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

İfadenin başındaki karat (`^`) eşleştirmenin URL yolunun başında başladığı anlamına gelir.

Yönlendirme kuralı ile önceki örnekte, `redirect-rule/(.*)`regex başında hiçbir`^`karat () var. Bu nedenle, herhangi bir `redirect-rule/` karakter başarılı bir eşleşme için yolda önce olabilir.

| Yol                               | Eşleştirme |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Evet   |
| `/my-cool-redirect-rule/1234/5678` | Evet   |
| `/anotherredirect-rule/1234/5678`  | Evet   |

Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)`yalnızca ' ile `rewrite-rule/`başlarlarsa yollar eşleşir. Aşağıdaki tabloda, eşleştirme farkı dikkat edin.

| Yol                              | Eşleştirme |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Evet   |
| `/my-cool-rewrite-rule/1234/5678` | Hayır    |
| `/anotherrewrite-rule/1234/5678`  | Hayır    |

İfadenin `^rewrite-rule/` bir bölümünü takiben, iki `(\d+)/(\d+)`yakalama grubu vardır. Bir `\d` basamak *(sayı) eşleşmesi*anlamına gelen. Artı işareti`+`( ) *önceki karakterin bir veya daha fazla eşleşmesi*anlamına gelir. Bu nedenle, URL bir sayı ve ardından başka bir sayı takip eden bir sayı içermelidir. Bu yakalama grupları yeniden yazılan URL'ye `$2`. `$1` Yeniden yazma kuralı değiştirme dizesi, yakalanan grupları sorgu dizesine yerleştirir. İstenen `/rewrite-rule/1234/5678` yol, kaynağı n ' `/rewritten?var1=1234&var2=5678`den elde etmek için yeniden yazılır. Özgün istekte bir sorgu dizesi varsa, URL yeniden yazıldığında korunur.

Kaynağı elde etmek için sunucuya gidiş dönüş yok. Kaynak varsa, *200 - Tamam* durum koduyla getirilir ve istemciye döndürülür. İstemci yönlendirilmediği için tarayıcının adres çubuğundaki URL değişmez. İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamamaktadır.

> [!NOTE]
> Eşleşen `skipRemainingRules: true` kurallar hesaplama olarak pahalı olduğundan ve uygulama yanıt süresini artırdığından, mümkün olduğunca kullanın. En hızlı uygulama yanıtı için:
>
> * En sık eşleşen kuraldan en sık eşleşen kurala yeniden yazma kurallarını sırala.
> * Eşleşme oluştuğunda ve ek kural işleme gerektirildiğinde kalan kuralların işlenmesini atlayın.

### <a name="apache-mod_rewrite"></a>Apaçi mod_rewrite

Apache mod_rewrite kurallarını <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>. Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun. Daha fazla bilgi ve mod_rewrite kuralları örnekleri için, [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)bakın.

<xref:System.IO.StreamReader> A, *ApacheModRewrite.txt* kuralları dosyasındaki kuralları okumak için kullanılır:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Örnek uygulama istekleri ' `/apache-mod-rules-redirect/(.\*)` den `/redirected?id=$1`' e yönlendirir. Yanıt durum kodu *302 - Bulundu.*

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Orijinal İstek:`/apache-mod-rules-redirect/1234`

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* Remote_addr
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>IIS URL Yeniden Yazma Modülü kuralları

IIS URL Yeniden Yazma Modülü için geçerli olan aynı <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>kural kümesini kullanmak için . Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun. Windows Server IIS'de çalışırken ara yazılımı uygulamanın *web.config* dosyasını kullanmaya yönlendirmeyin. IIS ile bu kurallar, IIS Yeniden Yazma modülüyle çakışmaları önlemek için uygulamanın *web.config* dosyasının dışında saklanmalıdır. IIS URL Yeniden Yazma Modülü kuralları hakkında daha fazla bilgi [ve](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) örnekler için [bkz.](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)

<xref:System.IO.StreamReader> A, *IISUrlRewrite.xml* kuralları dosyasındaki kuralları okumak için kullanılır:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Örnek uygulama isteklerini `/iis-rules-rewrite/(.*)` 'den ' e `/rewritten?id=$1`yeniden yazar Yanıt istemciye *200 - Tamam* durum koduyla gönderilir.

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Orijinal İstek:`/iis-rules-rewrite/1234`

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

Uygulamanızı istenmeyen şekillerde etkileyecek şekilde yapılandırılan sunucu düzeyinde kurallara sahip etkin bir IIS Yeniden Yazma Modülünüz varsa, bir uygulama için IIS Yeniden Yazma Modül'ünden devre dışı kullanabilirsiniz. Daha fazla bilgi için Bkz. [IIS modüllerini devre dışı bırakma.](xref:host-and-deploy/iis/modules#disabling-iis-modules)

#### <a name="unsupported-features"></a>Desteklenmeyen özellikler

Core 2.x ile ASP.NET yayımlanan ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü özelliklerini desteklemez:

* Giden Kuralları
* Özel Sunucu Değişkenleri
* Joker karakterler
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Desteklenen sunucu değişkenleri

Ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü sunucu değişkenlerini destekler:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* Remote_addr
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Ayrıca bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>üzerinden elde edebilirsiniz . Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir. Yeniden yazma kuralları dosyalarınızın sağladığınız yolda sunucuya dağıtıldığınızdan emin olun.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Yöntem tabanlı kural

Bir <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> yöntemde kendi kural mantığınızı uygulamak için kullanın. `Add`<xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, yönteminizde kullanıma hazır <xref:Microsoft.AspNetCore.Http.HttpContext> hale getirir. [Yeniden Yazma Bağlamı.Sonuç,](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) ek boru hattı işlemenin nasıl işleneceğini belirler. Değeri aşağıdaki tabloda <xref:Microsoft.AspNetCore.Rewrite.RuleResult> açıklanan alanlardan birine ayarlayın.

| `RewriteContext.Result`              | Eylem                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(varsayılan) | Kuralları uygulamaya devam edin.                                         |
| `RuleResult.EndResponse`             | Kuralları uygulamayı bırakın ve yanıtı gönderin.                       |
| `RuleResult.SkipRemainingRules`      | Kuralları uygulamayı bırakın ve bağlamı bir sonraki ara yazılıma gönderin. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Örnek *uygulama.xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntem gösterir. Bir `/file.xml`istek için yapılırsa, istek `/xmlfiles/file.xml`. Durum kodu *301*olarak ayarlanır - Kalıcı olarak taşınır. Tarayıcı */xmlfiles/file.xml*için yeni bir istekte bulunursa, Statik Dosya Middleware *wwwroot/xmlfiles* klasöründen dosyayı istemciye sunar. Yeniden yönlendirme için yanıtın durum kodunu açıkça ayarlayın. Aksi takdirde, *200 - Tamam* durum kodu döndürülür ve yönlendirme istemcide oluşmaz.

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Bu yaklaşım istekleri yeniden yazabilir. Örnek *uygulama, wwwroot* klasöründen *file.txt* metin dosyasına hizmet vermek için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir. Statik Dosya Middleware güncelleştirilmiş istek yolunu temel alarak dosyaya hizmet vermektedir:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>IRule tabanlı kural

Arabirimi uygulayan bir sınıfta kural mantığını kullanmak için <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> <xref:Microsoft.AspNetCore.Rewrite.IRule> kullanın. `IRule`yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar. Uygulama sınıfınız, <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> yöntem için parametreleri geçirmenize olanak tanıyan bir oluşturucu içerebilir.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Örnek uygulamadaki parametrelerin değerleri için `extension` ve `newPath` çeşitli koşulları karşılamak için kontrol edilir. Bir `extension` değer içermelidir ve değeri *.png*, *.jpg*veya *.gif*olmalıdır. Geçerli `newPath` değilse, bir <xref:System.ArgumentException> atılır. *image.png*için bir istek yapılırsa, `/png-images/image.png`istek . *image.jpg*için bir istek yapılırsa, `/jpg-images/image.jpg`istek . Durum kodu *301*olarak ayarlanır - Kalıcı `context.Result` olarak taşınır ve işleme kurallarını durdurmak ve yanıtı göndermek için ayarlanır.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Orijinal İstek:`/image.png`

![Geliştirici Araçları ile tarayıcı penceresi image.png için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_png_requests.png)

Orijinal İstek:`/image.jpg`

![Geliştirici Araçları ile tarayıcı penceresi image.jpg için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Regex örnekleri

| Hedef | Regex String &<br>Eşle' nin Örneği | Yedek Dize &<br>Çıktı Örneği |
| ---- | ------------------------------- | -------------------------------------- |
| Yolu querystring'e yeniden yazma | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Şerit izleme eğik çizgi | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Sondaki eğik çizgiyi zorla | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Belirli istekleri yeniden yazmaktan kaçının | `^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`<br>Evet:`/resource.htm`<br>№:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| URL segmentlerini yeniden düzenleme | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| URL kesimini değiştirme | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [.NET içinde normal ifadeler](/dotnet/articles/standard/base-types/regular-expressions)
* [Normal ifade dili - hızlı başvuru](/dotnet/articles/standard/base-types/quick-ref)
* [Apaçi mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [Url Yeniden Yazma Modülü 2.0'ı kullanma (IIS için)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [URL Yeniden Yazma Modülü Yapılandırma Başvurusu](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [IIS URL Yeniden Modül Forumu](https://forums.iis.net/1152.aspx)
* [Basit bir URL yapısını koruyun](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 URL İpuçları ve Püf Noktaları Yeniden Yazma](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Kesmek ya da kesmemek için](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
