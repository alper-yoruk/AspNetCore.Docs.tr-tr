---
title: ASP.NET Core 'de anahtar yönetimi
author: rick-anderson
description: ASP.NET Core veri koruma anahtarı yönetim API 'Lerinin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 0f54ceedf3558fb4fa2349903461fdfdb4dc02e6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776909"
---
# <a name="key-management-in-aspnet-core"></a>ASP.NET Core 'de anahtar yönetimi

<a name="data-protection-implementation-key-management"></a>

Veri koruma sistemi, yükleri korumak ve korumayı kaldırmak için kullanılan ana anahtarların ömrünü otomatik olarak yönetir. Her anahtar dört aşamanın birinde bulunabilir:

* Oluşturuldu-anahtar, anahtar halkada mevcut ancak henüz etkinleştirilmemiş. Anahtar, bu anahtar halkasını kullanan tüm makinelere yaymaya bir şansa kadar, anahtarın, yeni koruma işlemleri için kullanılmaması gerekir.

* Etkin-anahtar halkada bulunur ve tüm yeni koruma işlemleri için kullanılmalıdır.

* Süresi doldu-anahtar, doğal ömrünü çalıştırmıştır ve artık yeni koruma işlemleri için kullanılmamalıdır.

* İptal edildi-anahtar tehlikeye alındı ve yeni koruma işlemleri için kullanılmamalıdır.

Oluşturulan, etkin ve süre dolma anahtarları, gelen yüklerin korumasını kaldırmak için kullanılabilir. İptal edilen anahtarlar varsayılan olarak yük korumasını kaldırmak için kullanılamaz, ancak uygulama geliştiricisi gerekirse [Bu davranışı geçersiz kılabilir](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) .

>[!WARNING]
> Geliştirici, anahtar halkadan bir anahtar silmeye (ör. dosya sisteminden karşılık gelen dosyayı silerek) izin verebilir. Bu noktada, anahtar tarafından korunan tüm veriler kalıcı olarak çözülemez ve iptal edilmiş anahtarlarla benzer şekilde acil durum geçersiz kılma yoktur. Bir anahtarın silinmesi gerçekten bozucu bir davranıştır ve bu nedenle veri koruma sistemi bu işlemi gerçekleştirmek için birinci sınıf API 'YI kullanıma sunmaz.

## <a name="default-key-selection"></a>Varsayılan anahtar seçimi

Veri koruma sistemi, yedekleme deposundan anahtar halkasını okuduğunda, anahtar halkasının bir "varsayılan" anahtarını bulmaya çalışır. Varsayılan anahtar, yeni koruma işlemleri için kullanılır.

Genel buluşsal yöntemi, veri koruma sisteminin anahtarı varsayılan anahtar olarak en son etkinleştirme tarihi ile seçtiği bir değer. (Sunucudan sunucuya saat eğmaya izin veren küçük bir faktör vardır.) Anahtarın süresi dolmuşsa veya iptal edildiğinde ve uygulama otomatik anahtar oluşturmayı devre dışı bırakılmamışsa, [anahtar süre sonu ve](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) sıralı ilke başına hemen etkinleştirme ile yeni bir anahtar oluşturulur.

Veri koruma sisteminin farklı bir anahtara geri dönmesi yerine hemen yeni bir anahtar üretmesinin nedeni, yeni anahtar oluşturmanın yeni anahtardan önce etkinleştirilen tüm anahtarların örtük bir süre sonu olarak değerlendirilmesidir. Genel fikir, yeni anahtarların farklı algoritmalarla veya eski anahtarlardan gelen Rest mekanizmalarıyla yapılandırılmış olabileceği ve sistem geçerli yapılandırmanın geri düşmesini tercih etmelidir.

Bir özel durum var. Uygulama geliştiricisi [otomatik anahtar oluşturmayı devre dışı bırakıldıysa](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), veri koruma sisteminin varsayılan anahtar olarak bir şey seçmesi gerekir. Bu geri dönüş senaryosunda, sistem, kümedeki diğer makinelere yaymak üzere süresi geçen anahtarlara verilen tercihle, en son etkinleştirme tarihi ile iptal edilmemiş anahtarı seçer. Geri dönüş sistemi, zaman aşımına uğradı bir varsayılan anahtarın bir sonucu olarak seçilerek bitemeyebilir. Geri dönüş sistemi hiçbir şekilde varsayılan anahtar olarak iptal edilmiş bir anahtar seçmeyecektir ve anahtar halkası boşsa veya her anahtar iptal edildiğinde, sistem başlatma sırasında bir hata üretir.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Anahtar süre sonu ve yuvarlama

Bir anahtar oluşturulduğunda, otomatik olarak {Now + 2 gün} etkinleştirme tarihi ve {Now + 90 gün} tarihinde sona erme tarihi verilir. Etkinleştirmeden önce 2 günlük gecikme, sistem aracılığıyla yaymaya yönelik anahtar zamanına izin verir. Yani, yedekleme deposuna işaret eden diğer uygulamaların, sonraki otomatik yenileme döneminde anahtarı gözlemleyecek şekilde, anahtar halkasının etkin hale gelmesi durumunda kullanması gerekebilecek tüm uygulamalara yayıldığı ihtimalini en üst düzeye çıkarır.

Varsayılan anahtar 2 gün içinde sona erecektir ve anahtar halkasının varsayılan anahtarın süresi dolduktan sonra etkin olacak bir anahtarı yoksa, veri koruma sistemi otomatik olarak anahtar halkasının yeni bir anahtarını kalıcı hale alır. Bu yeni anahtar, {default anahtarının sona erme tarihi} için bir etkinleştirme tarihi ve {Now + 90 gün} tarihinde sona erme tarihi içeriyor. Bu, sistemin hiçbir hizmet kesintisi olmadan otomatik olarak anahtarları düzenli olarak almasına olanak tanır.

Anında etkinleştirme ile bir anahtarın oluşturulacağı durumlar olabilir. Bir örnek, uygulamanın bir süre boyunca çalıştırılmadığı zaman ve anahtar halkadaki tüm anahtarların süresi dolduğunda olacaktır. Bu durumda, anahtara normal 2 günlük etkinleştirme gecikmesi olmadan {Now} etkinleştirme tarihi verilir.

Varsayılan anahtar yaşam süresi 90 gündür, ancak bu, aşağıdaki örnekte olduğu gibi yapılandırılabilir.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Bir yönetici varsayılan sistem genelinde de değişiklik yapabilir, ancak ' a açık bir çağrı `SetDefaultKeyLifetime` , sistem genelinde herhangi bir ilkeyi geçersiz kılar. Varsayılan anahtar yaşam süresi 7 günden daha kısa olamaz.

## <a name="automatic-key-ring-refresh"></a>Otomatik anahtar halkasını yenileme

Veri koruma sistemi başlatıldığında, temel alınan depodan anahtar halkasını okur ve bellekte önbelleğe alır. Bu önbellek, yedekleme deposuna girmeden devam etmek için koruma ve kaldırma işlemleri yapılmasına izin verir. Sistem, her 24 saatte bir veya geçerli varsayılan anahtarın ne zaman dolacağını, ne olursa olsun, değişiklikler için yedekleme deposunu otomatik olarak denetler.

>[!WARNING]
> Geliştiricilerin, anahtar yönetim API 'Lerini doğrudan kullanması gerekir (her zaman). Veri koruma sistemi, yukarıda açıklandığı şekilde otomatik anahtar yönetimi gerçekleştirecek.

Veri koruma sistemi, anahtar halkasını `IKeyManager` incelemek ve değişiklikler yapmak için kullanılabilecek bir arabirim sunar. Örneğini `IDataProtectionProvider` sağlayan dı sistemi, tüketiminizi `IKeyManager` için bir örneği de sağlayabilir. Alternatif olarak, aşağıdaki örnekte `IKeyManager` `IServiceProvider` olduğu gibi düz bir şekilde çekebilirsiniz.

Anahtar halkasını değiştiren tüm işlemler (açıkça veya iptal işlemi gerçekleştirerek), bellek içi önbelleği geçersiz kılar. Bir sonraki `Protect` veya `Unprotect` çağrısı, veri koruma sisteminin anahtar halkasını yeniden kullanmasına ve önbelleğin yeniden oluşturulmasına neden olur.

Aşağıdaki örnek, mevcut anahtarları iptal `IKeyManager` etme ve yeni bir anahtarı el ile oluşturma dahil olmak üzere anahtar halkasını incelemek ve işlemek için arabirimin kullanımını gösterir.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Anahtar depolama

Veri koruma sistemi, uygun bir anahtar depolama konumunu ve geri kalan şifreleme mekanizmasını otomatik olarak getirmeye çalıştığını bir buluşsal yöntem içerir. Anahtar Kalıcılık mekanizması, uygulama geliştiricisi tarafından da yapılandırılabilir. Aşağıdaki belgeler, bu mekanizmaların yerleşik uygulamalarını tartışır:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
