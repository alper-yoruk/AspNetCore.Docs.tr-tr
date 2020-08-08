---
title: ASP.NET Core veri koruma
author: rick-anderson
description: Veri koruma kavramı ve ASP.NET Core veri koruma API 'Lerinin tasarım ilkeleri hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/introduction
ms.openlocfilehash: b29711e8e1ef1558731ba58ca2ff14000af19ca2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019362"
---
# <a name="aspnet-core-data-protection"></a>ASP.NET Core veri koruma

Web uygulamalarının genellikle güvenliğe duyarlı verileri depolaması gerekir. Windows Masaüstü uygulamaları için DPAPI sağlar, ancak bu Web uygulamaları için uygun değildir. ASP.NET Core veri koruma yığını, bir geliştiricinin anahtar yönetimi ve döndürme dahil olmak üzere verileri korumak için kullanabileceği basit ve kullanımı kolay bir şifreleme API 'SI sağlar.

ASP.NET Core veri koruma yığını, &lt; &gt; ASP.NET 1. x-4. x içindeki machineKey öğesi için uzun süreli değiştirme işlevi görecek şekilde tasarlanmıştır. Bu, eski şifreleme yığınının birçok eksikine yönelik olarak tasarlanmıştır ve bu da çoğu kullanım durumunda Modern uygulamaların karşılaştığı büyük bir çözüm sunar.

## <a name="problem-statement"></a>Sorun bildirimi

Genel sorun açıklaması tek bir tümcede succinctly belirtilebilir: daha sonra alımı sağlamak için güvenilir bilgileri kalıcı hale getirmeniz gerekiyor, ancak Kalıcılık mekanizmasına güvenmiyor. Web koşullarında bu, "güvenilmeyen bir istemci aracılığıyla güvenilir duruma gidiş dönüş olması gerekir." şeklinde yazılmış olabilir.

Bunun kurallı örneği, bir kimlik doğrulama cookie veya taşıyıcı belirteçtir. Sunucu bir "I ÖÖ ve XYZ izinleri var" belirtecini oluşturur ve istemciye ister. Daha sonraki bir tarihte, istemci bu belirteci sunucuya geri sunacaktır, ancak sunucunun belirteci sahte olmadığı bir tür güvence ihtiyacı vardır. Bu nedenle, ilk gereksinim: özgünlük (deyişle bütünlük, yetkisiz sağlama).

Kalıcı duruma sunucu tarafından güvenildiğinden, bu durumun işletim ortamına özgü bilgiler içerebileceğini tahmin ederiz. Bu, bir dosya yolu, izin, bir tanıtıcı veya başka bir dolaylı başvuru biçiminde veya sunucuya özgü başka bir veri parçası olabilir. Bu tür bilgiler genellikle güvenilmeyen bir istemciye açıklanmamalıdır. Bu nedenle, ikinci gereksinim: Gizlilik.

Son olarak, modern uygulamalar bileşen olduğundan, gördük, tek tek bileşenlerin, sistemdeki diğer bileşenlere bakılmaksızın bu sistemden yararlanmak ister. Örneğin, bir taşıyıcı belirteç bileşeni bu yığını kullanıyorsa aynı yığını da kullanan bir anti-CSRF mekanizmasından parazit olmadan çalışır. Bu nedenle son gereksinim: yalıtım.

Gereksinimlerimizin kapsamını daraltmak için daha fazla kısıtlama sağlayabiliriz. //Tr.wikipedia.org/wiki/RSA içinde çalışan tüm hizmetlerin eşit olarak güvenilir olduğunu ve verilerin doğrudan denetimizin altında oluşturulmaları veya hizmetlerin dışında harcanması gerektiğini varsaytık. Ayrıca, Web hizmetine yönelik her istek//tr.wikipedia.org/wiki/RSA 'e bir veya daha fazla kez gidebilecek olduğundan, bu işlemlerin mümkün olduğunca hızlı olmasını gerektiririz. Bu, simetrik şifrelemeyi senaryolarımız için ideal hale getirir ve bu tür bir süre kadar asimetrik şifrelemeyi indirimleriz.

## <a name="design-philosophy"></a>Tasarım felseü

Mevcut yığın ile ilgili sorunları tanımlayarak başladık. Bunu yaptıktan sonra, mevcut çözümlerin Yatayı gözettik ve mevcut bir çözüm, yalnızca bir çok aranan yeteneğe sahip değil. Daha sonra çeşitli temel ilkelere göre bir çözüm sunuyoruz.

* Sistemin yapılandırma basitliği sağlaması gerekir. İdeal olarak, sistem sıfır-yapılandırma olabilir ve geliştiriciler bir yerden çalışır. Geliştiricilerin belirli bir yönü (anahtar deposu gibi) yapılandırması gereken durumlarda, söz konusu yapılandırmaların basit hale getirilmesi için dikkate alınması gerekir.

* Basit bir tüketiciye yönelik API sunar. API 'Lerin düzgün şekilde kullanılması kolay ve yanlış kullanılması zor olmalıdır.

* Geliştiriciler anahtar yönetim ilkelerini öğrenmemelidir. Sistem, geliştirici adına algoritma seçimini ve anahtar yaşam süresini işlemelidir. İdeal olarak, geliştirici ham anahtar malzemesine asla erişemez.

* Anahtarlar mümkün olduğunda Rest 'te korunmalıdır. Sistem uygun bir varsayılan koruma mekanizmasını göstermelidir ve bunu otomatik olarak uygular.

Bu ilkeler göz önünde bulundurularak basit ve kullanımı [kolay](xref:security/data-protection/using-data-protection) bir veri koruma yığını geliştirdik.

ASP.NET Core veri koruma API 'Leri öncelikle gizli yüklerin sınırsız kalıcılığı için tasarlanmamıştır. [WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi) ve [Azure Rights Management](/rights-management/) gibi diğer teknolojiler, sınırsız depolama senaryosuna daha uygundur ve bunlara karşılık olarak güçlü anahtar yönetim özelliklerine sahiptir. Yani, bir geliştiricinin gizli verilerin uzun süreli korunması için ASP.NET Core veri koruma API 'Lerini kullanmasını engelleyen bir şey yoktur.

## <a name="audience"></a>Hedef kitle

Veri koruma sistemi beş ana pakete bölünmüştür. Bu API 'lerin çeşitli yönleri üç ana hedef kitleye sahiptir;

1. [Tüketici API 'Lerine genel bakış](xref:security/data-protection/consumer-apis/overview) hedef uygulama ve çerçeve geliştiricileri.

   "Yığının nasıl çalıştığı veya nasıl yapılandırıldığı hakkında bilgi almak istemiyorum. API 'Leri başarılı bir şekilde kullanmanın yüksek olasılığa karşı basit bir şekilde bir işlem gerçekleştirmek istiyorum. "

2. [Yapılandırma API 'leri](xref:security/data-protection/configuration/overview) , uygulama geliştiricileri ve sistem yöneticileri için hedef.

   "Veri koruma sistemine ortamımın varsayılan olmayan yollar veya ayarlar gerektirdiğini söylemem gerekiyor."

3. Genişletilebilirlik API 'Leri, geliştiricilerin özel ilke uygulama ücretlendirme aşamasında hedeflemesini hedefler. Bu API 'lerin kullanımı nadir durumlarla ve deneyimli güvenlik özellikli geliştiricilerle sınırlı olacaktır.

   "Gerçekten benzersiz davranış gereksinimlerine sahip olduğumu sistem içindeki bir bileşenin tamamını değiştirmem gerekiyor. Gereksinimlerimi yerine getiren bir eklenti oluşturmak için API yüzeyinin yaygın olarak kullanılan parçalarını öğrenmek istiyorum. "

## <a name="package-layout"></a>Paket düzeni

Veri koruma yığını beş paketten oluşur.

* [Microsoft. AspNetCore. DataProtection. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) , <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> veri koruma hizmetleri oluşturmak için ve arabirimlerini içerir. Ayrıca, bu türlerle çalışmak için yararlı genişletme yöntemleri içerir (örneğin, [ıdataprotector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Veri koruma sisteminin başka bir yerde örneği varsa ve API, başvuru kullanıyorsanız `Microsoft.AspNetCore.DataProtection.Abstractions` .

* [Microsoft. aspnetcore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) , temel şifreleme işlemleri, anahtar yönetimi, yapılandırma ve genişletilebilirlik dahil olmak üzere veri koruma sisteminin temel uygulamasını içerir. Veri koruma sisteminin örneğini oluşturmak (örneğin, bir öğesine eklemek <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ) veya davranışını değiştirmek ya da uzatmak için başvuru `Microsoft.AspNetCore.DataProtection` .

* [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) , geliştiricilerin yararlı bulabileceği ancak çekirdek pakete ait olmayan ek API 'leri içerir. Örneğin, bu paket, anahtarları bağımlılık ekleme (bkz.) olmadan dosya sistemindeki bir konumda depolamak için veri koruma sisteminin örneğini oluşturmaya yönelik Fabrika yöntemleri içerir <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . Ayrıca, korumalı yüklerin ömrünü kısıtlamak için uzantı yöntemleri de içerir (bkz <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector> .).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) , `<machineKey>` Yeni ASP.NET Core Data Protection yığınını kullanmak üzere işlemlerini yeniden yönlendirmek için mevcut bir ASP.NET 4. x uygulamasına yüklenebilir. Daha fazla bilgi için bkz. <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft. AspNetCore. Cryptography. Keytüretme](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) , PBKDF2 Password karma yordamının bir uygulamasını sağlar ve Kullanıcı parolalarını güvenli bir şekilde işlemesi gereken sistemler tarafından kullanılabilir. Daha fazla bilgi için bkz. <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Ek kaynaklar

<xref:host-and-deploy/web-farm>
