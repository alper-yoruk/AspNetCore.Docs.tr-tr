---
Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="overview-of-aspnet-core-security"></a>ASP.NET Core güvenliğine genel bakış

ASP.NET Core, geliştiricilerin uygulamaları için güvenliği kolayca yapılandırıp yönetmesine olanak sağlar. ASP.NET Core, kimlik doğrulama, yetkilendirme, veri koruma, HTTPS zorlaması, uygulama parolaları, istek önleyici koruma ve CORS yönetimi yönetimi için özellikler içerir. Bu güvenlik özellikleri, güçlü ancak güvenli ASP.NET Core uygulamalar oluşturmanıza olanak tanır.

## <a name="aspnet-core-security-features"></a>ASP.NET Core güvenlik özellikleri

ASP.NET Core, uygulamalarınızı güvenli hale getirmek için yerleşik kimlik sağlayıcıları da dahil olmak üzere birçok araç ve kitaplık sağlar, ancak Facebook, Twitter ve LinkedIn gibi üçüncü taraf kimlik hizmetlerini de kullanabilirsiniz. ASP.NET Core ile, uygulama gizli dizilerini kolayca yönetebilirsiniz. Bu, özel bilgileri kodda kullanıma sunmak zorunda kalmadan depolamanın ve kullanmanın bir yoludur.

## <a name="authentication-vs-authorization"></a>Kimlik doğrulama ve yetkilendirme karşılaştırması

Kimlik doğrulaması, bir kullanıcının bir işletim sistemi, veritabanı, uygulama veya kaynakta depolananlara kıyasla kimlik bilgileri sağladığı bir işlemdir. Eşleşiyorsa, kullanıcılar başarıyla kimlik doğrular ve yetkilendirme işlemi sırasında, için yetkilendirildiği eylemleri gerçekleştirebilir. Yetkilendirme, bir kullanıcının ne yapmasına izin verileceğini belirleyen işlemi ifade eder.

Kimlik doğrulamayı düşünmenin bir diğer yolu da, bir sunucu, veritabanı, uygulama veya kaynak gibi bir boşluk girmek için bir yöntem olarak göz önünde bulundurmaktır. yetkilendirme sırasında kullanıcının bu alanın içindeki hangi nesneleri gerçekleştirebileceği (sunucu, veritabanı veya uygulama) eylemleri.

## <a name="common-vulnerabilities-in-software"></a>Yazılımda yaygın olarak karşılaşılan güvenlik açıkları

ASP.NET Core ve EF, uygulamalarınızın güvenliğini sağlamanıza ve güvenlik ihlallerinin engellenmesine yardımcı olan özellikler içerir. Aşağıdaki bağlantı listesi, Web Apps 'teki en yaygın güvenlik açıklarını önlemenize yönelik teknik bilgi için sizi belgeler halinde yönlendirir:

* [Siteler arası betik saldırıları](xref:security/cross-site-scripting)
* [SQL ekleme saldırıları](/ef/core/querying/raw-sql)
* [Çapraz site Isteği forgery (CSRF)](xref:security/anti-request-forgery)
* [Yeniden yönlendirme saldırılarını aç](xref:security/preventing-open-redirects)

Bilmeniz gereken daha fazla güvenlik açığı vardır. Daha fazla bilgi için içindekiler tablosunun **güvenlik ve Identity ** bölümündeki diğer makalelere bakın.
