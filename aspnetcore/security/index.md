---
title: ASP.NET Çekirdek Güvenliğine Genel Bakış
author: rick-anderson
description: ASP.NET Core'da kimlik doğrulama, yetkilendirme ve güvenlik temelleri hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 0f8e96fb7d5246e746b95f8907745f849de60e24
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656039"
---
# <a name="overview-of-aspnet-core-security"></a>ASP.NET Çekirdek Güvenliğine Genel Bakış

ASP.NET Core, geliştiricilerin uygulamalarının güvenliğini kolayca yapılandırmalarına ve yönetmelerine olanak tanır. ASP.NET Core, kimlik doğrulama, yetkilendirme, veri koruma, HTTPS zorlama, uygulama sırları, istek karşıtı sahtecilik koruması ve CORS yönetimini yönetmek için özellikler içerir. Bu güvenlik özellikleri, güçlü ama güvenli ASP.NET Core uygulamaları oluşturmanıza olanak tanır.

## <a name="aspnet-core-security-features"></a>ASP.NET Temel güvenlik özellikleri

ASP.NET Core, yerleşik Kimlik sağlayıcıları da dahil olmak üzere uygulamalarınızı güvence altına almak için birçok araç ve kitaplık sağlar, ancak Facebook, Twitter veya LinkedIn gibi üçüncü taraf kimlik hizmetlerini kullanabilirsiniz. core ASP.NET ile, gizli bilgileri kodda ifşa etmek zorunda kalmadan depolamanın ve kullanmanın bir yolu olan uygulama sırlarını kolayca yönetebilirsiniz.

## <a name="authentication-vs-authorization"></a>Kimlik doğrulama ve Yetkilendirme

Kimlik doğrulama, kullanıcının daha sonra bir işletim sistemi, veritabanı, uygulama veya kaynakta depolananlarla karşılaştırılan kimlik bilgileri sağladığı bir işlemdir. Eşleşirlerse, kullanıcılar kimlik doğrulaması başarıyla gerçekleştirir ve yetkilendirme işlemi sırasında yetkilendirilen eylemleri gerçekleştirebilir. Yetkilendirme, kullanıcının ne yapmasına izin verilebilen işlemi ifade eder.

Kimlik doğrulaması düşünmenin başka bir yolu da, bunu sunucu, veritabanı, uygulama veya kaynak gibi bir alana girmenin bir yolu olarak değerlendirmektir, yetkilendirme ise kullanıcının o alandaki nesnelere (sunucu, veritabanı veya uygulama) hangi eylemleri gerçekleştirebileceğidir.

## <a name="common-vulnerabilities-in-software"></a>Yazılımdaki Yaygın Güvenlik Açıkları

ASP.NET Core ve EF, uygulamalarınızı güvenli hale almanıza ve güvenlik ihlallerini önlemenize yardımcı olan özellikler içerir. Aşağıdaki bağlantı listesi sizi web uygulamalarında en yaygın güvenlik açıklarından kaçınmak için ayrıntılı teknikler olarak belgelendirmeye götürür:

* [Site arası komut dosyası saldırıları](xref:security/cross-site-scripting)
* [SQL ekleme saldırıları](/ef/core/querying/raw-sql)
* [Site Arası İstek Sahteciliği (CSRF)](xref:security/anti-request-forgery)
* [Yeniden yönlendirme saldırılarını aç](xref:security/preventing-open-redirects)

Farkında olmanız gereken daha fazla güvenlik açığı vardır. Daha fazla bilgi için, içindekiler tablosunun **Güvenlik ve Kimlik** bölümündeki diğer makalelere bakın.
