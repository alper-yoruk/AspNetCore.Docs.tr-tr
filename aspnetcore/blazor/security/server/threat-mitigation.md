---
title: ASP.NET Core için tehdit azaltma Kılavuzu Blazor Server
author: guardrex
description: Uygulamalara yönelik güvenlik tehditlerini nasıl azaltacağınızı öğrenin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/05/2020
no-loc:
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: a9bdf68e30d2688d4d7836410a7913794a73fe01
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626434"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-no-locblazor-server"></a>ASP.NET Core için tehdit azaltma Kılavuzu Blazor Server

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor Server uygulamalar, sunucu ve istemcinin uzun süreli bir ilişki korumasını gerektiren, *durum bilgisi olan* bir veri işleme modelini benimseyin. Kalıcı durum, büyük olasılıkla uzun süreli bağlantılara yayılabilen bir [devre](xref:blazor/state-management)tarafından korunur.

Bir Kullanıcı bir siteyi ziyaret ettiğinde Blazor Server sunucu, sunucunun belleğinde bir devre oluşturur. Devre, kullanıcının Kullanıcı ARABIRIMINDE bir düğme seçtiğinde olduğu gibi olaylara hangi içeriğin işleneceğini ve yanıt verdiğini tarayıcıya gösterir. Bu işlemleri gerçekleştirmek için, devre bir bağlantı, kullanıcının tarayıcısında ve .NET yöntemlerinde JavaScript işlevlerini çağırır. Bu iki yönlü JavaScript tabanlı etkileşim, [JavaScript birlikte çalışma (js birlikte çalışma)](xref:blazor/call-javascript-from-dotnet)olarak adlandırılır.

JS birlikte çalışması Internet üzerinden yapıldığından ve istemci uzak bir tarayıcı kullandığından, Blazor Server Uygulamalar çoğu Web uygulaması güvenlik sorunlarını paylaşır. Bu konu, uygulamalara yönelik yaygın tehditleri açıklar Blazor Server ve Internet 'e yönelik uygulamalara odaklanan tehdit azaltma kılavuzu sağlar.

Şirket ağları veya intranetleri gibi kısıtlı ortamlarda, azaltma yönerglarından bazıları şunlardır:

* Kısıtlanmış ortamda uygulanmaz.
* Güvenlik riski kısıtlı bir ortamda azaldığından, uygulama maliyeti değer değildir.

## <a name="no-locblazor-and-shared-state"></a>Blazor ve paylaşılan durum

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>Kaynak tükenmesi

İstemci sunucuyla etkileşime geçtiğinde kaynak tükenmesi gerçekleşebilir ve sunucunun aşırı kaynak kullanmasına neden olur. Aşırı kaynak tüketimi öncelikle şunları etkiler:

* [CPU](#cpu)
* [Bellek](#memory)
* [İstemci bağlantıları](#client-connections)

Hizmet reddi (DoS) saldırıları genellikle bir uygulamanın veya sunucunun kaynaklarını tüketme konusunda arama yapılır. Ancak, kaynak tükenmesi sistem üzerinde bir saldırının sonucu değildir. Örneğin, yüksek Kullanıcı talebi nedeniyle sınırlı kaynaklar tükenebilir. DoS, [hizmet reddi (DOS) saldırıları](#denial-of-service-dos-attacks) bölümünde daha fazla ele alınmıştır.

BlazorVeritabanları ve dosya tutamaçları (dosyaları okumak ve yazmak için kullanılır) gibi, çerçeveye ait dış kaynaklar da kaynak tükenmesi ile karşılaşabilir. Daha fazla bilgi için bkz. <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

Bir veya daha fazla istemci, yoğun CPU işi gerçekleştirmeye çalışan bir veya daha fazla istemci tarafından meydana gelebilir.

Örneğin, Blazor Server bir *Fibonnacci numarasını*hesaplayan uygulamayı düşünün. Bir Fibonnacci numarası, dizideki her bir sayının önceki iki sayının toplamı olduğu bir Fibonnacci sırasından oluşturulur. Yanıta ulaşmak için gereken iş miktarı, sıranın uzunluğuna ve ilk değerin boyutuna bağlıdır. Uygulama bir istemcinin isteğine sınır yerleştirmezse, CPU yoğunluklu hesaplamalar CPU 'nun süresini ayırt edebilir ve diğer görevlerin performansını azalrlar. Aşırı kaynak tüketimi, kullanılabilirliği etkileyen bir güvenlik konusudur.

CPU tükenmesi, herkese açık olan tüm uygulamalar için bir sorun teşkil etmez. Normal Web uygulamalarında, istekler ve bağlantılar bir güvenlik önlemi olarak zaman aşımına uğrar, ancak Blazor Server uygulamalar aynı korumaları sağlamaz. Blazor Server uygulamalar, büyük olasılıkla CPU yoğunluklu iş yapmadan önce uygun denetimleri ve limitleri içermelidir.

### <a name="memory"></a>Bellek

Bir veya daha fazla istemci, sunucuyu büyük miktarda bellek kullanmaya zorlmaya zorlarsanız bellek tükenmesi meydana gelebilir.

Örneğin, bir Blazor öğe listesini kabul eden ve görüntüleyen bir bileşen ile sunucu tarafı uygulamasını düşünün. Uygulama, Blazor izin verilen öğe sayısı veya istemciye geri işlenen öğe sayısı için sınır yerleştirmezse, bellek yoğun işleme ve işleme sunucunun performansını sunucunun performansının bulunduğu noktaya ayırt edebilir. Sunucu kilitlenmişse veya çöktüğünde göründüğü noktadan yavaş olabilir.

Sunucuda olası bir bellek tükenmesi senaryosuna ait öğelerin listesini sürdürmek ve görüntülemek için aşağıdaki senaryoyu göz önünde bulundurun:

* Bir `List<MyItem>` özellik veya alandaki öğeler sunucunun belleğini kullanır. Uygulama, öğelerin listesinin sınırsız olarak büyümesine izin veriyorsa, sunucunun belleği tükenmeye karşı bir risk vardır. Belleğin tükenmesinin geçerli oturum sonlandırmasına (kilitlenme) ve bu sunucu örneğindeki tüm eşzamanlı oturumlara bir bellek dışı özel durum almasına neden olur. Bu senaryonun oluşmasını önlemek için, uygulamanın eşzamanlı kullanıcılara bir öğe sınırı uygulayan bir veri yapısı kullanması gerekir.
* Bir sayfalama şeması işleme için kullanılmazsa, sunucu Kullanıcı arabiriminde görünmeyen nesneler için ek bellek kullanır. Öğe sayısı sınırı olmadan, bellek talepleri kullanılabilir sunucu belleğini tüketebilir. Bu senaryoyu engellemek için aşağıdaki yaklaşımlardan birini kullanın:
  * İşleme sırasında sayfalandırılmış listeler kullanın.
  * Yalnızca ilk 100 ' i 1.000 öğeyi görüntüleyin ve kullanıcının görüntülenen öğelerin ötesinde öğeleri bulmak için arama ölçütü girmesini gerektirir.
  * Daha gelişmiş bir işleme senaryosu için *sanallaştırmayı*destekleyen listeler veya kılavuzlar uygulayın. Sanallaştırma kullanarak, listeler yalnızca kullanıcıya şu anda görünür olan öğelerin bir alt kümesini işler. Kullanıcı ARABIRIMDEKI ScrollBar ile etkileşime geçtiğinde, bileşen yalnızca görüntüleme için gereken öğeleri işler. Şu anda görüntülenmek üzere gerekli olmayan öğeler, en ideal yaklaşım olan ikincil depolamada tutulabilir. Görüntülenmezler olmayan öğeler bellekte tutulabilir ve bu da daha az idealdir.

Blazor Server uygulamalar, WPF, Windows Forms veya gibi durum bilgisi olan uygulamalar için diğer kullanıcı arabirimi çerçevelerine benzer bir programlama modeli sunar Blazor WebAssembly . Ana fark, uygulama tarafından tüketilen belleğin, istemciye ait olduğu ve yalnızca o tek istemciyi etkilediği bazı Kullanıcı arabirimi çerçevelerinden biridir. Örneğin, bir Blazor WebAssembly uygulama tamamen istemcide çalışır ve yalnızca istemci bellek kaynaklarını kullanır. Blazor ServerSenaryoda, uygulama tarafından tüketilen bellek sunucuya aittir ve sunucu örneğindeki istemciler arasında paylaşılır.

Sunucu tarafı bellek taleplerine tüm uygulamalar göz önünde bulundurulmalıdır Blazor Server . Ancak, çoğu Web uygulaması durum bilgisiz olur ve bir isteği işlerken kullanılan bellek, yanıt döndürüldüğünde serbest bırakılır. Genel bir öneri olarak, istemcilerin, istemci bağlantılarını devam eden diğer tüm sunucu tarafı uygulamalarda olduğu gibi ilişkisiz miktarda bellek ayırmasına izin vermez. Bir uygulama tarafından tüketilen bellek, Blazor Server tek bir istekten daha uzun bir süre devam ettirir.

> [!NOTE]
> Geliştirme sırasında, bir profil oluşturucu kullanılabilir veya istemci bellek taleplerini değerlendirmek için yakalanan bir izleme olabilir. Profil Oluşturucu veya izleme, belirli bir istemciye ayrılan belleği yakalamaz. Geliştirme sırasında belirli bir istemcinin bellek kullanımını yakalamak için, bir döküm yakalayın ve Kullanıcı devresi içinde kök olan tüm nesnelerin bellek talebini inceleyin.

### <a name="client-connections"></a>İstemci bağlantıları

Bir veya daha fazla istemci sunucuya çok fazla eş zamanlı bağlantı açtıklarında, diğer istemcilerin yeni bağlantı kurmasını engellediğinden bağlantı tükenmesi meydana gelebilir.

Blazor istemciler, oturum başına tek bir bağlantı kurar ve tarayıcı penceresi açık olduğu sürece bağlantıyı açık halde tutar. Tüm bağlantıları koruma sunucusundaki talepler uygulamalara özgü değildir Blazor . Bağlantıların kalıcı doğası ve uygulamaların durum bilgisi olan doğası göz önüne alındığında Blazor Server , bağlantı tükenmesi uygulamanın kullanılabilirliğine daha fazla risk taşır.

Varsayılan olarak, bir uygulama için Kullanıcı başına bağlantı sayısı sınırı yoktur Blazor Server . Uygulama bir bağlantı sınırı gerektiriyorsa aşağıdaki yaklaşımlardan birini veya daha fazlasını yapın:

* Yetkisiz kullanıcıların uygulamaya bağlanma yeteneğini doğal olarak sınırlayan kimlik doğrulaması gerektir. Bu senaryonun etkili olabilmesi için kullanıcıların, ' de Yeni Kullanıcı sağlaması engellenmelidir.
* Kullanıcı başına bağlantı sayısını sınırlayın. Bağlantıları sınırlandırma, aşağıdaki yaklaşımlar aracılığıyla gerçekleştirilebilir. Meşru kullanıcıların uygulamaya erişmesine izin vermeye özen gösterin (örneğin, istemcinin IP adresine göre bir bağlantı sınırı oluşturulduğunda).
  * Uygulama düzeyinde:
    * Uç nokta yönlendirme genişletilebilirliği.
    * Uygulamaya bağlanmak ve Kullanıcı başına etkin oturumları izlemek için kimlik doğrulaması gerektir.
    * Sınıra ulaştıktan sonra yeni oturumları reddedin.
    * İstemcilerden bir uygulamaya bağlantı oluşturan [Azure SignalR hizmeti](/azure/azure-signalr/signalr-overview) gibi bir ara sunucu aracılığıyla uygulamaya yönelik proxy WebSocket bağlantıları. Bu, tek bir istemcinin yapabileceğinden daha fazla bağlantı kapasitesine sahip bir uygulama sağlar ve istemcinin sunucu bağlantılarını tüketmesini önler.
  * Sunucu düzeyinde: uygulamanın önünde bir proxy/ağ geçidi kullanın. Örneğin, [Azure ön kapısı](/azure/frontdoor/front-door-overview) , Web trafiğinin bir uygulamaya küresel olarak yönlendirilmesini tanımlamanıza, yönetmenize ve izlemenize olanak sağlar.

## <a name="denial-of-service-dos-attacks"></a>Hizmet reddi (DoS) saldırıları

Hizmet reddi (DoS) saldırıları, istemcinin bir veya daha fazla kaynağın bir veya daha fazla uygulamayı tüketmesine neden olan bir istemciyi içerir. Blazor Server uygulamalar, bazı varsayılan limitleri içerir ve SignalR açık olan DOS saldırılarına karşı korumak için diğer ASP.NET Core ve limitlere güvenir <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> .

| Blazor Server uygulama sınırı | Açıklama | Varsayılan |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | Belirli bir sunucunun bellekte tek seferde tuttuğu bağlantı kesilen en fazla bağlantı sayısı. | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | Bağlantısı kesilmiş bir devre dışı bırakılmadan önce bellekte tutulan en fazla süre. | 3 dakika |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | Zaman uyumsuz bir JavaScript işlev çağrısını zaman aşımına uğramadan önce sunucunun bekleyeceği en fazla süre. | 1 dakika |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | En fazla bildirilmemiş işleme toplu işi sayısı sunucu, güçlü yeniden bağlanmayı desteklemek için belirli bir zamanda her bir devreye göre bellekte kalır. Sınıra ulaştıktan sonra sunucu, bir veya daha fazla toplu iş istemci tarafından onaylanana kadar yeni oluşturma toplu işleri oluşturmayı durduruyor. | 10 |

Tek bir gelen hub iletisinin en büyük ileti boyutunu ile ayarlayın <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> .

| SignalR ve ASP.NET Core sınırı | Açıklama | Varsayılan |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | Tek bir ileti için ileti boyutu. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Tarayıcıyla etkileşimler (istemci)

İstemci, JS birlikte çalışma olayı gönderme ve işleme tamamlama aracılığıyla sunucuyla etkileşime girer. JS birlikte çalışma iletişimi, JavaScript ve .NET arasında her iki yolla da geçer:

* Tarayıcı olayları istemciden sunucuya zaman uyumsuz biçimde gönderilir.
* Sunucu, gerektiği şekilde kullanıcı arabiriminden zaman uyumsuz olarak rerendering.

### <a name="javascript-functions-invoked-from-net"></a>.NET 'ten çağrılan JavaScript işlevleri

.NET yöntemlerinden JavaScript 'e yapılan çağrılar için:

* Tüm etkinleştirmeleri, başarısız olduktan sonra, arayana döndüren yapılandırılabilir bir zaman aşımı süresine sahiptir <xref:System.OperationCanceledException> .
  * Bir dakikalık çağrılar () için varsayılan bir zaman aşımı süresi vardır <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> . Bu sınırı yapılandırmak için bkz <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> ..
  * İptal belirtecini çağrı başına temelinde denetlemek için bir iptal belirteci sağlayabilirsiniz. Bir iptal belirteci sağlandıysa, mümkün olan ve istemciye yapılan tüm çağrıların zaman içinde sağlandığı varsayılan çağrı zaman aşımını kullanır.
* JavaScript çağrısının sonucu güvenilir olamaz. BlazorTarayıcıda çalışan uygulama istemcisi çağırmak Için JavaScript işlevini arar. İşlev çağrılır ve sonuç ya da bir hata oluşturulur. Kötü amaçlı bir istemci şunları gerçekleştirmeye çalışabilir:
  * JavaScript işlevinden bir hata döndürerek uygulamada sorun oluşmasına neden olur.
  * JavaScript işlevinden beklenmeyen bir sonuç döndürerek sunucuda istemeden bir davranış alır.

Yukarıdaki senaryolara karşı koruma için aşağıdaki önlemleri alın:

* [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)Çağırma sırasında oluşabilecek hataları hesaba eklemek için DEYIMLERDEKI js birlikte çalışma çağrılarını sarın. Daha fazla bilgi için bkz. <xref:blazor/fundamentals/handle-errors#javascript-interop>.
* Herhangi bir işlem yapmadan önce, hata iletileri de dahil olmak üzere JS birlikte çalışma çağırmaları tarafından döndürülen verileri doğrulayın.

### <a name="net-methods-invoked-from-the-browser"></a>Tarayıcıdan çağrılan .NET yöntemleri

JavaScript 'e yönelik çağrılara .NET yöntemlerine güvenmeyin. JavaScript 'e bir .NET yöntemi sunulduğunda, .NET yönteminin nasıl çağrılacağını göz önünde bulundurun:

* Uygulamaya genel bir uç nokta gibi, JavaScript 'e sunulan tüm .NET metodunu değerlendirin.
  * Girişi doğrula.
    * Değerlerin beklenen aralıklar içinde olduğundan emin olun.
    * Kullanıcının istenen eylemi gerçekleştirme izni olduğundan emin olun.
  * .NET Yöntem çağırma kapsamında aşırı miktarda kaynak ayırmayın. Örneğin, denetim gerçekleştirin ve CPU ve bellek kullanımı için sınır koyun.
  * Statik ve örnek yöntemlerinin JavaScript istemcilerine sunutabileceğiniz hesaba sahip olun. Tasarım, uygun kısıtlamalarla durum paylaşma için çağrı yaptığı müddetçe, oturumlar arasında durum paylaşmaktan kaçının.
    * `DotNetReference`İlk olarak bağımlılık ekleme (dı) aracılığıyla oluşturulan nesneler aracılığıyla kullanıma sunulan örnek yöntemleri için nesnelerin kapsamlı nesneler olarak kaydedilmesi gerekir. Bu, uygulamanın kullandığı tüm DI Hizmetleri için geçerlidir Blazor Server .
    * Statik yöntemler için, uygulama bir sunucu örneğindeki tüm kullanıcılar genelinde durum tasarımını özel olarak paylaşmadığı müddetçe, istemciye kapsamdaki durumu oluşturmaktan kaçının.
  * Parametrelerde Kullanıcı tarafından sağlanan verileri JavaScript çağrılarına geçirmekten kaçının. Parametrelerde veri geçirilmesi kesinlikle gerekliyse, JavaScript kodunun, [siteler arası betik oluşturma (XSS)](#cross-site-scripting-xss) güvenlik açıklarına gerek kalmadan verileri geçirmeyi işlediğinden emin olun. Örneğin, bir öğenin özelliğini ayarlayarak Belge Nesne Modeli (DOM) Kullanıcı tarafından sağlanan verileri yazma `innerHTML` . Devre dışı bırakmak için [Içerik güvenlik ilkesi 'ni (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) `eval` ve diğer güvenli olmayan JavaScript temel öğelerini kullanmayı düşünün.
* Framework 'ün gönderme uygulamasının en üstünde .NET etkinleştirmeleri için özel bir dağıtma uygulamaktan kaçının. .NET yöntemlerini tarayıcıya sunma, genel geliştirme için önerilmeyen gelişmiş bir senaryodur Blazor .

### <a name="events"></a>Ekinlikler

Olaylar bir uygulamaya giriş noktası sağlar Blazor Server . Web Apps 'teki uç noktaları koruma için aynı kurallar, uygulamalardaki olay işleme için geçerlidir Blazor Server . Kötü amaçlı bir istemci, istediği verileri bir olay için yük olarak gönderebilirler.

Örnek:

* Bir için değişiklik olayı `<select>` , uygulamanın istemciye sunulan seçenekler içinde olmayan bir değer gönderebilir.
* `<input>`Sunucu, istemci tarafı doğrulamayı atlayarak herhangi bir metin verisi gönderebilir.

Uygulamanın, uygulamanın işlediği herhangi bir olay için verileri doğrulaması gerekir. BlazorFramework [Forms bileşenleri](xref:blazor/forms-validation) temel doğrulamaları gerçekleştirir. Uygulama özel form bileşenleri kullanıyorsa, olay verilerinin uygun şekilde doğrulanması için özel kodun yazılması gerekir.

Blazor Server olaylar zaman uyumsuzdur, bu nedenle uygulamanın yeni bir işleme oluşturarak tepki verme süresi geçmeden önce sunucuya birden çok olay gönderilebilir. Göz önünde bulundurulması gereken bazı güvenlik etkileri vardır. Uygulamadaki istemci eylemlerinin sınırlandırmasının, olay işleyicileri içinde gerçekleştirilmesi ve geçerli işlenen görünüm durumuna bağlı olmaması gerekir.

Bir kullanıcının bir sayacı en fazla üç kez artmasını sağlayan bir sayaç bileşeni düşünün. Sayacı artırma düğmesi şu değere göre koşullu olarak belirlenir `count` :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Bir istemci, çerçeve bu bileşenin yeni bir işlemesini oluşturmadan önce bir veya daha fazla artış olayı gönderebilir. Bunun sonucunda, `count` düğme UI tarafından yeterince hızlı bir şekilde kaldırılmadığı için Kullanıcı tarafından *üç kez* arttırılabilirler. Üç artımdan oluşan sınırı elde etmenin doğru yolu `count` Aşağıdaki örnekte gösterilmiştir:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

`if (count < 3) { ... }`Denetimi işleyicinin içine ekleyerek, artırma kararı `count` geçerli uygulama durumuna göre belirlenir. Bu karar, önceki örnekte olduğu gibi Kullanıcı arabiriminin durumunu temel değildir ve bu da geçici olarak eski olabilir.

### <a name="guard-against-multiple-dispatches"></a>Birden çok gönderine karşı koruma

Bir olay geri çağırması, bir dış hizmetten veya veritabanından veri getirme gibi uzun süre çalışan bir işlemi zaman uyumsuz olarak çağıralıyorsa, bir koruyucu kullanmayı düşünün. Koruyucu, bir işlem görsel geri bildirimde çalışırken, kullanıcının birden çok işlemi sıraya almasını önleyebilir. Aşağıdaki bileşen kodu, `isLoading` `true` sunucudan verileri alırken olarak ayarlanır `GetForecastAsync` . `isLoading`Olduğunda `true` , düğme Kullanıcı arabiriminde devre dışı bırakılır:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Önceki örnekte gösterilen koruma deseninin arka plan işlemi, düzeniyle zaman uyumsuz olarak yürütülürse, bu işlem geçerlidir `async` - `await` .

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Erken iptali yapın ve bir-After-Dispose kullanmaktan kaçının

[Birden çok gönderilerde koruma](#guard-against-multiple-dispatches) bölümünde açıklandığı gibi bir koruyucu kullanmanın yanı sıra, <xref:System.Threading.CancellationToken> bileşen bırakıldığında uzun süre çalışan işlemleri iptal etmek için bir kullanın. Bu yaklaşımda, bileşenlerden *sonra kullanım-sonrasında Dispose özelliğinden kaçınmanın* sağladığı avantaj vardır:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Büyük miktarlarda veri üreten olaylardan kaçının

Veya gibi bazı DOM olayları `oninput` `onscroll` büyük miktarda veri üretebilir. Bu olayları Blazor sunucu uygulamalarında kullanmaktan kaçının.

## <a name="additional-security-guidance"></a>Ek güvenlik kılavuzu

ASP.NET Core uygulamalarının güvenliğini sağlamaya yönelik rehberlik, uygulamalar için geçerlidir Blazor Server ve aşağıdaki bölümlerde ele alınmıştır:

* [Günlüğe kaydetme ve hassas veriler](#logging-and-sensitive-data)
* [HTTPS ile yoldaki bilgileri koruma](#protect-information-in-transit-with-https)
* [Siteler arası betik oluşturma (XSS)](#cross-site-scripting-xss)
* [Çapraz kaynak koruması](#cross-origin-protection)
* [Tıklama-Jacking](#click-jacking)
* [Yeniden yönlendirmeleri aç](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Günlüğe kaydetme ve hassas veriler

İstemci ve sunucu arasındaki JS birlikte çalışma etkileşimleri, sunucu günlüklerine <xref:Microsoft.Extensions.Logging.ILogger> örneklerle kaydedilir. Blazor gerçek olaylar veya JS birlikte çalışma girişleri ve çıkışları gibi hassas bilgileri günlüğe kaydetmeyi önler.

Sunucuda bir hata oluştuğunda, çerçeve istemciye bildirir ve oturumu kapatır. Varsayılan olarak, istemci tarayıcının geliştirici araçlarında görünebileceğini belirten genel bir hata iletisi alır.

İstemci tarafı hatası, çağrı yığınını içermez ve hatanın nedeni hakkında ayrıntı sağlamaz, ancak sunucu günlükleri bu gibi bilgileri içerir. Geliştirme amacıyla, önemli hata bilgileri, ayrıntılı hataları etkinleştirerek istemciye kullanılabilir hale getirilebilir.

JavaScript 'te ayrıntılı hataları etkinleştir:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* `DetailedErrors`Olarak ayarlanan yapılandırma anahtarı `true` , uygulama ayarları dosyasında ( `appsettings.json` ) ayarlanabilir. Anahtar, `ASPNETCORE_DETAILEDERRORS` bir değeri olan ortam değişkeni kullanılarak da ayarlanabilir `true` .

> [!WARNING]
> Internet 'teki istemcilere hata bilgilerini ortaya çıkarmak her zaman kaçınılması gereken bir güvenlik riskidir.

### <a name="protect-information-in-transit-with-https"></a>HTTPS ile yoldaki bilgileri koruma

Blazor ServerSignalRistemci ve sunucu arasındaki iletişimi kullanır. Blazor Server Normalde, genellikle WebSockets olan bir aktarımı kullanır SignalR .

Blazor Server sunucu ile istemci arasında gönderilen verilerin bütünlüğünü ve gizliliğini garanti etmez. Her zaman HTTPS kullanın.

### <a name="cross-site-scripting-xss"></a>Siteler arası betik oluşturma (XSS)

Siteler arası betik oluşturma (XSS), yetkisiz bir tarafın tarayıcı bağlamında rastgele mantık yürütmesine olanak sağlar. Güvenliği aşılmış bir uygulama, istemcide rastgele kod çalıştırabilir. Güvenlik açığı, sunucuda büyük olasılıkla çok sayıda kötü amaçlı eylem gerçekleştirmek için kullanılabilir:

* Sahte/geçersiz olayları sunucuya gönderme.
* Dağıtım başarısız/geçersiz işleme tamamlama.
* İşleme tamamlamamasını gönderdikten kaçının.
* JavaScript 'ten .NET 'e birlikte çalışma çağrıları gönderme.
* .NET 'ten JavaScript 'e birlikte çalışma çağrılarının yanıtını değiştirme.
* .NET ile JS birlikte çalışma sonuçlarına dağıtma kullanmaktan kaçının.

Blazor ServerFramework, önceki tehditlere karşı korumak için gereken adımları gerçekleştirir:

* İstemci, işleme toplu işlerini bildirmeden, yeni UI güncellemeleri oluşturmayı durduruyor. İle yapılandırılır <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> .
* İstemciden bir yanıt almadan bir dakikadan sonra herhangi bir .NET için JavaScript çağrısı süresi. İle yapılandırılır <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> .
* JS birlikte çalışması sırasında tarayıcıdan gelen tüm girişte temel doğrulama gerçekleştirir:
  * .NET başvuruları geçerli ve .NET yöntemi tarafından beklenen türde.
  * Veriler hatalı biçimlendirilmemiş.
  * Yöntem için doğru sayıda bağımsız değişken, yükte bulunur.
  * Yöntemi çağırmadan önce bağımsız değişkenler veya sonuç doğru şekilde seri durumdan çıkarılmış olabilir.
* Tarayıcıdan gönderilen olaylardan gelen tüm girişte temel doğrulama gerçekleştirir:
  * Olayın geçerli bir türü vardır.
  * Olay verilerinin serisi kaldırılamaz.
  * Olayla ilişkili bir olay işleyicisi var.

Framework 'ün uyguladığı korumalarına ek olarak, tehditlere karşı korumak ve uygun işlemleri gerçekleştirmek için uygulamanın geliştirici tarafından kodlanmış olması gerekir:

* Olayları işlerken her zaman verileri doğrulayın.
* Geçersiz veri aldıktan sonra uygun eylemi gerçekleştirin:
  * Verileri yoksayın ve döndürün. Bu, uygulamanın istekleri işlemeye devam etmesine izin verir.
  * Uygulama girişin meşru olduğunu belirlerse ve meşru istemci tarafından üretilemeyecek bir özel durum oluşturun. Bir özel durum oluşturmak devre dışı olarak oturum kapatır ve oturumu sonlandırır.
* Günlüklere dahil olan işleme toplu işlemleri tarafından sağlanan hata iletisine güvenmeyin. Hata istemci tarafından sağlanır ve istemcinin güvenliği tehlikeye aşmış olabileceğinden genellikle güvenilemez.
* JS birlikte çalışma çağrılarında, JavaScript ve .NET yöntemleri arasında her iki yönde de girişe güvenmeyin.
* Bağımsız değişkenlerin veya sonuçların doğru şekilde seri durumdan çıkarılsa bile, uygulama bağımsız değişkenlerin ve sonuçların içeriğinin geçerli olduğunu doğrulamaktan sorumludur.

Bir XSS Güvenlik açığının mevcut olması için, uygulamanın işlenen sayfada Kullanıcı girişini içermesi gerekir. Blazor Server bileşenler bir `.razor` dosyadaki biçimlendirmenin yordamsal C# mantığına dönüştürülebileceği bir derleme zamanı adımı yürütür. Çalışma zamanında C# mantığı öğeleri, metni ve alt bileşenleri açıklayan bir *işleme ağacı* oluşturur. Bu, tarayıcı DOM 'a bir JavaScript yönergeleri dizisi aracılığıyla uygulanır (veya prerendering durumunda HTML olarak serileştirilir):

* Normal sözdizimi aracılığıyla işlenen Kullanıcı girişi Razor (örneğin, `@someStringValue` ), Razor sözdizimi yalnızca metin YAZABILECEĞINDEN komutlar aracılığıyla Dom 'A eklendiğinden bir XSS Güvenlik Açığı sunmaz. Değer HTML biçimlendirmesi içerse bile, değer statik metin olarak görüntülenir. Prerendering olduğunda çıktı HTML kodlamalı olur ve bu da içeriği statik metin olarak görüntüler.
* Betik etiketlerine izin verilmez ve uygulamanın bileşen işleme ağacına dahil edilmemelidir. Bir komut dosyası etiketi bir bileşenin biçimlendirmesinde yer alıyorsa, derleme zamanı hatası oluşturulur.
* Bileşen yazarları, kullanmadan C# içindeki bileşenleri yazabilir Razor . Bileşen yazarı, çıkış yayırken doğru API 'Leri kullanmaktan sorumludur. Örneğin, `builder.AddContent(0, someUserSuppliedString)` *not* `builder.AddMarkupContent(0, someUserSuppliedString)` ikinci olarak bir XSS Güvenlik Açığı oluşturmasından dolayı değil kullanın.

XSS saldırılarına karşı koruma kapsamında, [Içerik güvenlik ilkesi (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)gibi XSS azaltmalarını gerçekleştirmeyi düşünün.

Daha fazla bilgi için bkz. <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Çapraz kaynak koruması

Çapraz kaynak saldırıları, sunucuya yönelik bir eylem gerçekleştiren farklı bir kaynaktan gelen bir istemciyi içerir. Kötü amaçlı eylem, genellikle bir GET isteği veya bir form GÖNDERISINI (siteler arası Istek sahteciliği, CSRF), ancak kötü amaçlı bir WebSocket açmak da mümkündür. Blazor Server uygulamalar, [ SignalR hub protokolünü kullanan diğer tüm uygulamaların aynısını](xref:signalr/security)sunar:

* Blazor Server uygulamalara ek ölçüler alınana kadar, uygulamalara çapraz kaynak erişilebilir. Çapraz kaynak erişimini devre dışı bırakmak için, işlem hattındaki CORS 'yi işlem hattına ekleyerek ve <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> Blazor uç nokta meta verilerine ekleyerek veya [ SignalR arası kaynak paylaşımı için yapılandırarak](xref:signalr/security#cross-origin-resource-sharing)izin verilen çıkış noktaları kümesini sınırlandırarak devre dışı bırakın.
* CORS etkinse, CORS yapılandırmasına bağlı olarak uygulamayı korumak için ek adımlar gerekebilir. CORS genel olarak etkinleştirilmişse, Blazor Server <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> uç nokta yol Oluşturucu ' da çağrıldıktan sonra meta veri noktası meta verilerine eklenen CORS, Hub için devre dışı bırakılabilir.

Daha fazla bilgi için bkz. <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Tıklama-Jacking

Tıklama-Jacking `<iframe>` , kullanıcıyı saldırı kapsamındaki sitede eylemler gerçekleştirmeye ikna etmek için bir sitenin farklı bir kaynaktan bir site içinde işlenmesini içerir.

Bir uygulamanın içinde işlemesini korumak için `<iframe>` [Içerik güvenlik IlkesI (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) ve `X-Frame-Options` üst bilgi kullanın. Daha fazla bilgi için bkz. [MDN Web belgeleri: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Yeniden yönlendirmeleri aç

Bir Blazor Server uygulama oturumu başladığında, sunucu, oturum başlatma işleminin bir parçası olarak gönderilen URL 'lerin temel doğrulamasını gerçekleştirir. Framework, devre oluşturmadan önce temel URL 'nin geçerli URL 'nin bir üst olduğunu denetler. Framework tarafından başka denetim yapılmaz.

Kullanıcı istemcide bir bağlantı seçtiğinde, bağlantının URL 'SI sunucuya gönderilir ve bu işlem gerçekleştirilecek eylemi belirler. Örneğin, uygulama bir istemci tarafı gezintisi gerçekleştirebilir veya tarayıcıya yeni konuma gidemeyeceğini belirtebilir.

Bileşenler, program aracılığıyla aracılığıyla gezinme isteklerini de tetikleyebilirler <xref:Microsoft.AspNetCore.Components.NavigationManager> . Bu tür senaryolarda, uygulama bir istemci tarafı gezintisi gerçekleştirebilir veya tarayıcıya yeni konuma gidebileceğini gösterebilir.

Bileşenler:

* Gezinti çağrısı bağımsız değişkenlerinin bir parçası olarak Kullanıcı girişini kullanmaktan kaçının.
* Hedefin uygulama tarafından izin verildiğinden emin olmak için bağımsız değişkenleri doğrulayın.

Aksi takdirde, kötü niyetli bir kullanıcı tarayıcıyı saldırgan tarafından denetlenen bir siteye gitmesini zorlayabilir. Bu senaryoda, saldırgan, yöntemi çağırmanın bir parçası olarak uygulamayı bazı kullanıcı girişlerini kullanarak ' ye püf ediyor <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> .

Bu öneri, uygulamanın bir parçası olarak bağlantılar işlenirken de geçerlidir:

* Mümkünse, göreli bağlantıları kullanın.
* Mutlak bağlantı hedeflerinin bir sayfaya dahil etmeden önce geçerli olduğunu doğrulayın.

Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Güvenlik denetim listesi

Aşağıdaki güvenlik konuları listesi ayrıntılı değildir:

* Etkinliklerden bağımsız değişkenleri doğrulayın.
* Giriş ve, JS birlikte çalışma çağrılarındaki sonuçları doğrulayın.
* .NET için JS birlikte çalışabilirlik çağrılarına yönelik kullanıcı girişini kullanmaktan (veya önceden doğrulama) kaçının.
* İstemcinin ilişkisiz miktarda bellek ayırmasını engelleyin.
  * Bileşen içindeki veriler.
  * `DotNetObject` istemciye döndürülen başvurular.
* Birden çok gönderine karşı koruma.
* Bileşen atıldığı zaman uzun süre çalışan işlemleri iptal edin.
* Büyük miktarlarda veri üreten olaylardan kaçının.
* Kullanıcı girişini, çağrıların bir parçası olarak kullanmaktan kaçının <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ve daha önce kaçınılmaz bir şekilde, URL 'ler için Kullanıcı girişini, izin verilen kaynaklar kümesine göre doğrulayın.
* Kullanıcı arabiriminin durumuna göre yetkilendirme kararları yapmayın, ancak yalnızca bileşen durumudur.
* XSS saldırılarına karşı korunmak için [Içerik güvenlik ilkesi 'ni (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) kullanmayı düşünün.
* Tıklama-Jacking 'e karşı korumak için CSP ve [X çerçeve seçeneklerini](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) kullanmayı düşünün.
* CORS 'yi etkinleştirirken veya uygulamalar için CORS 'yi açıkça devre dışı bıraktığınızda CORS ayarlarının uygun olduğundan emin olun Blazor .
* Uygulamanın sunucu tarafı sınırlarının kabul Blazor edilemez bir risk düzeyi olmadan kabul edilebilir bir kullanıcı deneyimi sağlamasına emin olmak için test edin.
