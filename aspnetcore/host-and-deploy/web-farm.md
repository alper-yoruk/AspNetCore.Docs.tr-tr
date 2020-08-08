---
title: Web çiftliğinde ASP.NET Core ana bilgisayar
author: rick-anderson
description: Bir ASP.NET Core uygulamasının birden çok örneğini bir Web grubu ortamında paylaşılan kaynaklarla nasıl barındıracağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/web-farm
ms.openlocfilehash: 58409b5c47d71c96ece6f4ecfab6f18df47f798b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015445"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Web çiftliğinde ASP.NET Core ana bilgisayar

[Chris](https://github.com/Tratcher) 'e göre

Bir *Web grubu* , bir uygulamanın birden çok örneğini barındıran iki veya daha fazla Web sunucusu (veya *düğüm*) grubudur. Kullanıcılardan gelen istekler bir Web grubuna ulaştığında, *yük dengeleyici* istekleri Web grubunun düğümlerine dağıtır. Web gruplarının geliştirilmesi:

* **Güvenilirlik/kullanılabilirlik**: bir veya daha fazla düğüm başarısız olduğunda, yük dengeleyici istekleri işlemeye devam etmek için istekleri diğer çalışan düğümlere yönlendirebilir.
* **Kapasite/performans**: birden çok düğüm, tek bir sunucudan daha fazla istek işleyebilir. Yük dengeleyici istekleri düğümlere dağıtarak iş yükünü dengeler.
* **Ölçeklenebilirlik**: daha fazla veya daha az kapasite gerektiğinde, etkin düğümlerin sayısı artılarak iş yüküne uyacak şekilde artırılabilir veya azaltılabilir. [Azure App Service](https://azure.microsoft.com/services/app-service/)gibi Web grubu platformu teknolojileri, Sistem Yöneticisi isteğine otomatik olarak veya insan müdahalesi olmadan otomatik olarak düğüm ekleyebilir veya kaldırabilir.
* Bakım **: bir**Web grubunun düğümleri, daha kolay sistem yönetimi ile sonuçlanan bir dizi paylaşılan hizmete bağlı olabilir. Örneğin, bir Web grubunun düğümleri tek bir veritabanı sunucusunu ve görüntüler ve indirilebilir dosyalar gibi statik kaynaklar için ortak bir ağ konumunu temel alabilir.

Bu konuda, paylaşılan kaynaklara bağlı bir Web çiftliğinde barındırılan ASP.NET Core uygulamaları için yapılandırma ve bağımlılıklar açıklanmaktadır.

## <a name="general-configuration"></a>Genel yapılandırma

<xref:host-and-deploy/index>  
Barındırma ortamlarını ayarlamayı ve ASP.NET Core uygulamaları dağıtmayı öğrenin. Uygulama başlatma ve yeniden başlatma işlemlerini otomatik hale getirmek için Web grubunun her bir düğümünde bir işlem yöneticisi yapılandırın. Her düğüm ASP.NET Core çalışma zamanını gerektirir. Daha fazla bilgi için, belgelerin [ana bilgisayar ve dağıtım](xref:host-and-deploy/index) alanındaki konulara bakın.

<xref:host-and-deploy/proxy-load-balancer>  
Genellikle önemli istek bilgilerini gizleyen ara sunucu ve yük dengeleyiciler arkasında barındırılan uygulamalar için yapılandırma hakkında bilgi edinin.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) , Web uygulamalarını barındırmak için ASP.NET Core dahil olmak üzere bir [Microsoft bulut bilgi işlem platformu hizmetidir](https://azure.microsoft.com/) . App Service, otomatik ölçeklendirme, Yük Dengeleme, düzeltme eki uygulama ve sürekli dağıtım sağlayan, tam olarak yönetilen bir platformdur.

## <a name="app-data"></a>Uygulama verileri

Bir uygulama birden çok örneğe ölçeklenirse, düğümler arasında paylaşım gerektiren uygulama durumu olabilir. Durum geçicidir, bir [ıdistributedcache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache)paylaşmayı düşünün. Paylaşılan durum kalıcılığı gerektiriyorsa, paylaşılan durumu bir veritabanında depolamayı göz önünde bulundurun.

## <a name="required-configuration"></a>Gerekli yapılandırma

Veri koruma ve önbelleğe alma, bir Web grubuna dağıtılan uygulamalar için yapılandırma gerektirir.

### <a name="data-protection"></a>Veri Koruma

[ASP.NET Core Data Protection sistemi](xref:security/data-protection/introduction) , uygulamalar tarafından verileri korumak için kullanılır. Veri koruma, *anahtar halkaında*depolanan bir şifreleme anahtarı kümesini temel alır. Veri koruma sistemi başlatıldığında, anahtar halkasını yerel olarak depolayan [varsayılan ayarları](xref:security/data-protection/configuration/default-settings) uygular. Varsayılan yapılandırma altında, Web grubunun her bir düğümüne benzersiz bir anahtar halkası depolanır. Sonuç olarak, her Web grubu düğümü diğer düğümlerde bir uygulama tarafından şifrelenen verilerin şifresini çözemez. Varsayılan yapılandırma, uygulamaları bir Web grubunda barındırmak için genellikle uygun değildir. Paylaşılan anahtar halkasını uygulamaya bir alternatif, her zaman kullanıcı isteklerini aynı düğüme yönlendirkullanmaktır. Web grubu dağıtımları için veri koruma sistem yapılandırması hakkında daha fazla bilgi için bkz <xref:security/data-protection/configuration/overview> ..

### <a name="caching"></a>Önbelleğe alma

Bir Web grubu ortamında, önbelleğe alma mekanizması, önbelleğe alınmış öğeleri Web grubunun düğümleri arasında paylaşmalıdır. Önbelleğe alma, ortak bir Redsıs önbelleği, paylaşılan bir SQL Server veritabanı veya Web grubu genelinde önbelleğe alınmış öğeleri paylaşan özel bir önbelleğe alma uygulamasını kullanmalıdır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Bağımlı bileşenler

Aşağıdaki senaryolar ek yapılandırma gerektirmez, ancak Web grupları için yapılandırma gerektiren teknolojilere bağımlıdır.

| Senaryo | Bağlı&hellip; |
| -------- | ------------------- |
| Kimlik Doğrulaması | Veri koruma (bkz <xref:security/data-protection/configuration/overview> .).<br><br>Daha fazla bilgi için <xref:security/authentication/cookie> ve <xref:security/cookie-sharing> bölümlerine bakın. |
| Identity | Kimlik doğrulama ve veritabanı yapılandırması.<br><br>Daha fazla bilgi için bkz. <xref:security/authentication/identity>. |
| Oturum | Veri koruma (şifrelenmiş cookie ) (bkz <xref:security/data-protection/configuration/overview> .) ve önbelleğe alma (bkz <xref:performance/caching/distributed> .).<br><br>Daha fazla bilgi için bkz. [oturum ve durum yönetimi: oturum durumu](xref:fundamentals/app-state#session-state). |
| TempData | Veri koruma (şifreli cookie ) (bkz <xref:security/data-protection/configuration/overview> .) veya oturum (bkz. [oturum ve durum yönetimi: oturum durumu](xref:fundamentals/app-state#session-state)).<br><br>Daha fazla bilgi için bkz. [oturum ve durum yönetimi: TempData](xref:fundamentals/app-state#tempdata). |
| Korsanlığa karşı koruma | Veri koruma (bkz <xref:security/data-protection/configuration/overview> .).<br><br>Daha fazla bilgi için bkz. <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Sorun giderme

### <a name="data-protection-and-caching"></a>Veri koruma ve önbelleğe alma

Veri koruma veya önbelleğe alma bir Web grubu ortamı için yapılandırılmadığında, istekler işlendiğinde aralıklı hatalar oluşur. Bu durum, düğümlerin aynı kaynakları paylaşmadığı ve kullanıcı isteklerinin her zaman aynı düğüme geri yönlendirilmediği için oluşur.

Kimlik doğrulaması kullanarak uygulamada oturum açan bir kullanıcıyı göz önünde bulundurun cookie . Kullanıcı uygulamada bir Web grubu düğümünde oturum açar. Bir sonraki isteği, oturum açtıkları aynı düğüme alınırsa, uygulama kimlik doğrulamasının şifresini çözebilir cookie ve uygulamanın kaynağına erişim izni verebilir. Sonraki istekleri farklı bir düğüme alınırsa, uygulama cookie kullanıcının oturum açmadığı düğümden kimlik doğrulamasının şifresini çözemez ve istenen kaynak için yetkilendirme başarısız olur.

Aşağıdaki belirtilerden herhangi biri **zaman zaman**meydana geldiğinde, sorun genellikle hatalı veri korumasına veya bir Web grubu ortamı için önbelleğe alma yapılandırmasına göre izlenmelidir:

* Kimlik doğrulama sonları: kimlik doğrulaması cookie yanlış yapılandırılmış veya şifresi çözülemiyor. OAuth (Facebook, Microsoft, Twitter) veya Openıdconnect oturumları "bağıntı başarısız oldu" hatasıyla başarısız oluyor.
* Yetkilendirme sonları: Identity kaybedildi.
* Oturum durumu verileri kaybeder.
* Önbelleğe alınan öğeler kaybolur.
* TempData başarısız olur.
* Gönderi başarısız: koruma denetimi başarısız olur.

Web grubu dağıtımları için veri koruma yapılandırması hakkında daha fazla bilgi için bkz <xref:security/data-protection/configuration/overview> .. Web grubu dağıtımları için önbelleğe alma yapılandırması hakkında daha fazla bilgi için bkz <xref:performance/caching/distributed> ..

## <a name="obtain-data-from-apps"></a>Uygulamalardan veri alma

Web grubu uygulamalarının isteklere yanıt verme yeteneği varsa, Terminal satır içi ara yazılım kullanarak uygulamalardan istek, bağlantı ve ek verileri alın. Daha fazla bilgi ve örnek kod için bkz <xref:test/troubleshoot#obtain-data-from-an-app> ..

## <a name="additional-resources"></a>Ek kaynaklar

* [Windows Için özel Betik uzantısı](/azure/virtual-machines/extensions/custom-script-windows): dağıtım sonrası yapılandırma ve yazılım yüklemesi için yararlı olan Azure sanal makinelerinde betikleri indirir ve yürütür.
* <xref:host-and-deploy/proxy-load-balancer>
 