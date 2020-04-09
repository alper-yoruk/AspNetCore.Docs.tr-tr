---
title: Bir web çiftliğinde Host ASP.NET Core
author: rick-anderson
description: Bir web çiftliği ortamında paylaşılan kaynaklara sahip bir ASP.NET Core uygulamasının birden çok örneğini nasıl barındıracaklarını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/web-farm
ms.openlocfilehash: 316c87e5f49593c05991a94cbe5e55d175a49bb3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659371"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Bir web çiftliğinde Host ASP.NET Core

Yazar: [Chris Ross](https://github.com/Tratcher)

*Web çiftliği,* bir uygulamanın birden çok örneğini barındıran iki veya daha fazla web sunucusundan (veya *düğümden)* oluşan bir gruptur. Kullanıcılardan gelen istekler bir web çiftliğine geldiğinde, *yük dengeleyici* isteklerini web çiftliğinin düğümlerine dağıtır. Web çiftlikleri geliştirmek:

* **Güvenilirlik/kullanılabilirlik** &ndash; Bir veya daha fazla düğüm başarısız olduğunda, yük dengeleyici istekleri işlemeye devam etmek için istekleri diğer işleyen düğümlere yönlendirebilir.
* **Kapasite/performans** &ndash; Birden çok düğüm tek bir sunucudan daha fazla isteği işleyebilir. Yük dengeleyicisi, istekleri düğümlere dağıtarak iş yükünü dengeler.
* **Ölçeklenebilirlik** &ndash; Daha fazla veya daha az kapasite gerektiğinde, etkin düğüm sayısı iş yüküyle eşleşecek şekilde artırılabilir veya azaltılabilir. [Azure Uygulama Hizmeti](https://azure.microsoft.com/services/app-service/)gibi Web farm platform teknolojileri, sistem yöneticisinin isteği üzerine veya insan müdahalesi olmadan otomatik olarak düğümekleyebilir veya kaldırabilir.
* Bir web çiftliğinin **Sürdürülebilirlik** &ndash; Düğümleri, paylaşılan hizmetler kümesine güvenebilir ve bu da daha kolay sistem yönetimiyle sonuçlanır. Örneğin, bir web çiftliğinin düğümleri tek bir veritabanı sunucusuna ve görüntüler ve indirilebilir dosyalar gibi statik kaynaklar için ortak bir ağ konumuna güvenebilir.

Bu konu, paylaşılan kaynaklara dayanan bir web çiftliğinde barındırılan ASP.NET temel uygulamaların yapılandırması ve bağımlılıklarını açıklar.

## <a name="general-configuration"></a>Genel yapılandırma

<xref:host-and-deploy/index>  
Barındırma ortamlarını nasıl ayarlayıp Core uygulamalarını ASP.NET nasıl dağıttığınızı öğrenin. Uygulama nın başlatılmasını ve yeniden başlatılmasını otomatikleştirmek için web eksede her düğümde bir işlem yöneticisi ni yapılandırın. Her düğüm ASP.NET Çekirdek çalışma süresini gerektirir. Daha fazla bilgi için [Ana Bilgisayar'daki](xref:host-and-deploy/index) konulara bakın ve belgelerin dağıtıla alanındaki konulara bakın.

<xref:host-and-deploy/proxy-load-balancer>  
Proxy sunucuları ve yük dengeleyicileri arkasında barındırılan ve genellikle önemli istek bilgilerini gizleyen uygulamalar için yapılandırma hakkında bilgi edinin.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service,](https://azure.microsoft.com/services/app-service/) ASP.NET Core dahil olmak üzere web uygulamalarını barındırmak için bir [Microsoft bulut bilgi işlem platformu hizmetidir.](https://azure.microsoft.com/) Uygulama Hizmeti, otomatik ölçekleme, yük dengeleme, yama ve sürekli dağıtım sağlayan tam olarak yönetilen bir platformdur.

## <a name="app-data"></a>Uygulama verileri

Bir uygulama birden çok örneğine ölçeklendirildiğinde, düğümler arasında paylaşım gerektiren uygulama durumu olabilir. Durum geçiciyse, bir [IDistributedCache'yi](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache)paylaşmayı düşünün. Paylaşılan durum kalıcılık gerektiriyorsa, paylaşılan durumu bir veritabanında depolamayı düşünün.

## <a name="required-configuration"></a>Gerekli yapılandırma

Veri Koruma ve Önbelleğe alma, bir web çiftliğine dağıtılan uygulamalar için yapılandırma gerektirir.

### <a name="data-protection"></a>Veri Koruma

[ASP.NET Çekirdek Veri Koruma sistemi,](xref:security/data-protection/introduction) uygulamalar tarafından verileri korumak için kullanılır. Veri Koruması, *anahtarlıkta*depolanan bir dizi şifreleme anahtarına dayanır. Veri Koruma sistemi baş harfe döndüğünde, anahtarlık yerel olarak depolayan [varsayılan ayarları](xref:security/data-protection/configuration/default-settings) uygular. Varsayılan yapılandırma altında, web eksede her düğümde benzersiz bir anahtarlık depolanır. Sonuç olarak, her web çİt düğümü, başka bir düğümdeki bir uygulama tarafından şifrelenen verilerin şifresini çözemez. Varsayılan yapılandırma genellikle bir web çiftliğinde uygulamaları barındırmak için uygun değildir. Paylaşılan bir anahtarlık uygulamanın alternatifi, kullanıcı isteklerini her zaman aynı düğüme yönlendirmektir. Web çiftliği dağıtımları için Veri Koruma sistemi <xref:security/data-protection/configuration/overview>yapılandırması hakkında daha fazla bilgi için bkz.

### <a name="caching"></a>Önbelleğe alma

Önbelleğe alma mekanizması, web çiftliğinin düğümleri arasında önbelleğe alınmış öğeleri paylaşmalıdır. Önbelleğe alma, ortak bir Redis önbelleğine, paylaşılan bir SQL Server veritabanına veya önbelleğe alınmış öğeleri web çiftliğinde paylaşan özel bir önbelleğe alma uygulamasına dayanmalıdır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Bağımlı bileşenler

Aşağıdaki senaryolar ek yapılandırma gerektirmez, ancak web çiftlikleri için yapılandırma gerektiren teknolojilere bağlıdır.

| Senaryo | Bağlıdır&hellip; |
| -------- | ------------------- |
| Kimlik Doğrulaması | Veri Koruma <xref:security/data-protection/configuration/overview>(bkz. ).<br><br>Daha fazla bilgi için <xref:security/authentication/cookie> ve <xref:security/cookie-sharing> bölümlerine bakın. |
| Kimlik | Kimlik doğrulama ve veritabanı yapılandırması.<br><br>Daha fazla bilgi için bkz. <xref:security/authentication/identity>. |
| Oturum | Veri Koruması (şifreli çerezler) <xref:security/data-protection/configuration/overview>(bakınız) <xref:performance/caching/distributed>ve Önbelleğe Alma (bkz. ).<br><br>Daha fazla bilgi için [oturum ve durum yönetimi: Oturum durumu'](xref:fundamentals/app-state#session-state)na bakın. |
| Geçici Veriler | Veri Koruması (şifreli çerezler) <xref:security/data-protection/configuration/overview>(bkz. ) veya Oturum (bkz. [Oturum ve durum yönetimi: Oturum durumu).](xref:fundamentals/app-state#session-state)<br><br>Daha fazla bilgi için [Oturum ve devlet yönetimi: TempData'](xref:fundamentals/app-state#tempdata)ya bakın. |
| Sahtecilikle mücadele | Veri Koruma <xref:security/data-protection/configuration/overview>(bkz. ).<br><br>Daha fazla bilgi için bkz. <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Sorun giderme

### <a name="data-protection-and-caching"></a>Veri Koruma ve önbelleğe alma

Veri Koruma veya önbelleğe alma bir web çiftliği ortamı için yapılandırılmamışsa, istekler işlendiğinde aralıklı hatalar oluşur. Bu, düğümlerin aynı kaynakları paylaşmaması ve kullanıcı isteklerinin her zaman aynı düğüme yönlendirilen olmaması nedeniyle oluşur.

Çerez kimlik doğrulamasını kullanarak uygulamaya giriş yapan bir kullanıcı düşünün. Kullanıcı uygulamayı tek bir web çiftliği düğümünde işaretler. Bir sonraki istekleri oturum imzaladıkları aynı düğüme gelirse, uygulama kimlik doğrulama çerezinin şifresini çözebilir ve uygulamanın kaynağına erişim sağlar. Bir sonraki istekleri farklı bir düğüme gelirse, uygulama kimlik doğrulama çerezinin şifresini kullanıcının oturum imzaladığı düğümden çözemez ve istenen kaynak için yetkilendirme başarısız olur.

Aşağıdaki belirtilerden herhangi biri **aralıklı olarak**ortaya çıktığında, sorun genellikle bir web çiftliği ortamı için yanlış Veri Koruması veya önbelleğe alma yapılandırmasına kadar izlenir:

* Kimlik doğrulama &ndash; tatili Kimlik doğrulama çerezi yanlış yapılandırılmıştır veya şifresi çözülemez. OAuth (Facebook, Microsoft, Twitter) veya OpenIdConnect girişleri "Korelasyon başarısız" hatasıyla başarısız olur.
* Yetkilendirme &ndash; tatili Kimlik kaybolur.
* Oturum durumu veri kaybeder.
* Önbelleğe alınmış öğeler kaybolur.
* TempData başarısız olur.
* POST'lar &ndash; başarısız anti-sahteçek başarısız olur.

Web çiftliği dağıtımları için Veri Koruması yapılandırması hakkında daha fazla bilgi için bkz. <xref:security/data-protection/configuration/overview> Web farm dağıtımları için önbelleğe alma <xref:performance/caching/distributed>yapılandırması hakkında daha fazla bilgi için bkz.

## <a name="obtain-data-from-apps"></a>Uygulamalardan veri alma

Web çiftliği uygulamaları isteklere yanıt verebiliyorsa, terminal satır ara yazılımını kullanarak uygulamalardan istek, bağlantı ve ek veri alın. Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Windows](/azure/virtual-machines/extensions/custom-script-windows) &ndash; İndirmeler için Özel Komut Dosyası Uzantısı, dağıtım sonrası yapılandırma ve yazılım yüklemesi için kullanışlı olan Azure sanal makinelerinde komut dosyaları çalıştırır.
* <xref:host-and-deploy/proxy-load-balancer>
 