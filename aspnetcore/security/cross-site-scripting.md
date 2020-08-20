---
title: ASP.NET Core siteler arası komut dosyası (XSS) engelle
author: rick-anderson
description: ASP.NET Core uygulamasında bu güvenlik açığını gidermeye yönelik siteler arası betik oluşturma (XSS) ve teknikler hakkında bilgi edinin.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: ec8b321be08447ca634a1e28799f790f723f17d1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625628"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>ASP.NET Core siteler arası komut dosyası (XSS) engelle

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Siteler arası betik oluşturma (XSS), bir saldırganın istemci tarafı komut dosyalarını (genellikle JavaScript) Web sayfalarına yerleştirmesini sağlayan bir güvenlik açığıdır. Diğer kullanıcılar etkilenen sayfaları yüklediğine göre, saldırganın betikleri çalışacaktır ve saldırganın, oturum belirteçleri çalıştırmasına, cookie Web sayfasının IÇERIĞINI Dom düzenlemesi aracılığıyla değiştirmesine veya tarayıcıyı başka bir sayfaya yönlendirmenize olanak tanır. XSS Güvenlik Açıkları genellikle bir uygulama kullanıcı girişi aldığında ve bunu doğrulamadan, kodlamadan veya çıkarmadan bir sayfaya çıktılarken oluşur.

## <a name="protecting-your-application-against-xss"></a>Uygulamanızı XSS 'ye karşı koruma

Temel düzeyde XSS `<script>` , uygulamanıza, işlenmiş sayfanıza bir etiket ekleyerek veya bir olayı bir olaya ekleyerek çalışarak işe yarar `On*` . Geliştiriciler, uygulamaları için XSS 'ye giriş yapmaktan kaçınmak için aşağıdaki önleme adımlarını kullanmalıdır.

1. Aşağıdaki adımları takip etmediğiniz takdirde, güvenilmeyen verileri hiçbir şekilde HTML girişine yerleştirmeyin. Güvenilmeyen veriler, bir saldırgan olarak bir saldırgan, HTML form girişleri, sorgu dizeleri, HTTP üst bilgileri ve bir saldırgan tarafından denetlenebilecek veri kaynağı, uygulamanızı ihlal edemese bile veritabanınızı ihlal edebiliyor olabilir.

2. Güvenli olmayan verileri bir HTML öğesine geçirmeden önce, HTML kodlamalı olduğundan emin olun. HTML kodlaması gibi karakterler alır &lt; ve lt; gibi güvenli bir biçimde değiştirir &amp;

3. Güvenilir olmayan verileri HTML özniteliğine geçirmeden önce, HTML kodlamalı olduğundan emin olun. HTML öznitelik kodlaması, HTML kodlamasının bir üst kümesidir ve "ve" gibi ek karakterlerin kodlamasını belirler.

4. Güvenilir olmayan verileri JavaScript 'e geçirmeden önce, verileri çalışma zamanında aldığınız bir HTML öğesine yerleştirin. Bu mümkün değilse, verilerin JavaScript kodlamalı olduğundan emin olun. JavaScript kodlaması, JavaScript için tehlikeli karakterler alır ve bunları onaltılı olarak değiştirir. Örneğin, &lt; olarak kodlanır `\u003C` .

5. Güvenilmeyen verileri bir URL sorgu dizesine geçirmeden önce URL 'nin kodlandığından emin olun.

## <a name="html-encoding-using-no-locrazor"></a>Kullanarak HTML kodlaması Razor

RazorMVC 'de kullanılan motor, bunu yapmamaya gerçekten engel olmadığınız müddetçe, değişkenlerden kaynaklanan tüm çıktıyı otomatik olarak kodlar. Yönergesini her kullandığınızda HTML öznitelik kodlama kurallarını kullanır *@* . Html öznitelik kodlaması bir HTML kodlaması üst kümesidir, bu, HTML kodlaması veya HTML öznitelik kodlaması kullanılması gerekip gerekmediği için kendinize sorun olması gerekmediği anlamına gelir. Güvenilmeyen girişi doğrudan JavaScript 'e eklemeye çalışırken değil, yalnızca bir HTML içeriğinde @ kullandığınızdan emin olmanız gerekir. Etiket Yardımcıları, etiket parametrelerinde kullandığınız girişi de kodlayacaktır.

Aşağıdaki görünümü uygulayın Razor :

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Bu görünüm *Untrustedınput* değişkeninin içeriğini verir. Bu değişken, XSS saldırılarında kullanılan bazı karakterleri içerir, yani &lt; "ve &gt; . Kaynağı incelemek, işlenen çıktının şu şekilde kodlanmış olduğunu gösterir:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC, `HtmlString` Çıkış üzerine otomatik olarak kodlanmayan bir sınıf sağlar. Bu, XSS Güvenlik Açığı sunan güvenilir olmayan girişle birlikte asla kullanılmamalıdır.

## <a name="javascript-encoding-using-no-locrazor"></a>Kullanılarak JavaScript kodlaması Razor

Görünümünüzde işlemek için JavaScript 'e bir değer eklemek istediğiniz zaman olabilir. Bunu yapmanın iki yolu vardır. Değer eklemenin en güvenli yolu, değeri bir etiketinin veri özniteliğinde yerleştirmenin ve JavaScript 'te alamaktır. Örnek:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Bu, aşağıdaki HTML 'yi oluşturur

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Bu işlem çalıştığında, aşağıdakileri işleyecek:

```
<"123">
   <"123">
```

JavaScript Encoder 'ı doğrudan de çağırabilirsiniz:

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

Bu, tarayıcıda şu şekilde işlenir:

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> DOM öğeleri oluşturmak için, JavaScript 'te güvenilmeyen girişi birleştirme. Ya da gibi `createElement()` özellik değerlerini kullanmanız ve atamanız `node.TextContent=` ya da başka bir şekilde `element.SetAttribute()` / `element[attribute]=` kendi kendinize Dom tabanlı XSS 'de kullanıma sunacaksınız.

## <a name="accessing-encoders-in-code"></a>Koddaki kodlayıcılara erişme

HTML, JavaScript ve URL kodlayıcıları kodunuzda iki şekilde kullanılabilir, bunları [bağımlılık ekleme](xref:fundamentals/dependency-injection) yoluyla ekleyebilir veya ad alanında bulunan varsayılan kodlayıcıları kullanabilirsiniz `System.Text.Encodings.Web` . Varsayılan kodlayıcıları kullanırsanız, güvenli olarak değerlendirilecek karakter aralıklarına uyguladığınız her türlü, varsayılan kodlayıcılar mümkün olan en güvenli kodlama kurallarını kullanır.

Yapılandırıcılar aracılığıyla yapılandırılabilir kodlayıcıları kullanmak için, kurucularınız uygun şekilde bir *Htmlencoder*, *javascriptencoder* ve *URLEncoder* parametresi almalıdır. Örneğin;

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>Kodlama URL parametreleri

Değer olarak güvenilmeyen giriş içeren bir URL sorgu dizesi derlemek istiyorsanız, `UrlEncoder` değeri kodlamak için öğesini kullanın. Örneğin,

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Kodladıktan sonra, encodedValue değişkeni de içerecektir `%22Quoted%20Value%20with%20spaces%20and%20%26%22` . Boşluk, tırnak, noktalama ve diğer güvenli olmayan karakterler, onaltılık değerlerine göre yüzde olarak kodlanır, örneğin bir boşluk karakteri %20 olur.

>[!WARNING]
> Güvenilmeyen girişi, URL yolunun bir parçası olarak kullanmayın. Güvenilmeyen girişi her zaman bir sorgu dizesi değeri olarak geçirin.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Kodlayıcıları özelleştirme

Varsayılan olarak kodlayıcılar, temel Latin Unicode aralığıyla sınırlı bir güvenli liste kullanır ve karakter kodu eşdeğerleri olarak bu aralığın dışındaki tüm karakterleri kodlayabilir. Bu davranış Razor , Dizelerinizin çıktısını almak için kodlayıcıları kullanacağı için taghelper ve HtmlHelper işlemesini da etkiler.

Bunun arkasındaki nedenler bilinmeyen veya gelecekteki tarayıcı hataları karşısında korunmaktan (önceki tarayıcı hataları Ingilizce olmayan karakterlerin işlenmesine göre ayrıştırılır). Web siteniz Çince, Kiril veya diğerleri gibi Latin olmayan karakterlerin büyük bir bölümünü kullanıyorsa, bu durum muhtemelen istediğiniz davranış değildir.

' De başlangıç sırasında uygulamanıza uygun Unicode aralıklarını dahil etmek için Kodlayıcı güvenli listelerini özelleştirebilirsiniz `ConfigureServices()` .

Örneğin, varsayılan yapılandırmayı kullanarak, Razor benzer bir HtmlHelper kullanabilirsiniz;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Web sayfasının kaynağını görüntülediğinizde, Çince metin kodlamalı şekilde, aşağıdaki şekilde işlenmiş olduğunu görürsünüz;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Kodlayıcı tarafından güvenli olarak değerlendirilen karakterleri genişletmek için, içindeki yöntemine aşağıdaki satırı ekleyin `ConfigureServices()` `startup.cs` ;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Bu örnek, Cjkunifiedideograf Unicode aralığını dahil etmek için güvenli listeyi widens. Oluşturulan çıkış şimdi şu şekilde olur

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Güvenli liste aralıkları, diller değil Unicode kod grafikleri olarak belirtilir. [Unicode standardında](https://unicode.org/) , karakterlerinizi içeren grafiği bulmak için kullanabileceğiniz bir [kod grafikleri](https://www.unicode.org/charts/index.html) listesi bulunur. Her kodlayıcı, HTML, JavaScript ve URL 'Nin ayrı ayrı yapılandırılması gerekir.

> [!NOTE]
> Güvenli listenin özelleştirilmesi yalnızca DI aracılığıyla kaynak kodlayıcıları etkiler. Daha sonra varsayılan olarak bir kodlayıcıyla doğrudan erişmeniz durumunda `System.Text.Encodings.Web.*Encoder.Default` yalnızca temel Latin SafeList kullanılacaktır.

## <a name="where-should-encoding-take-place"></a>Kodlama nerede gerçekleşmelidir?

Genel kabul edilen uygulama, kodlamanın çıkış noktasında gerçekleşmesi ve kodlanmış değerlerin hiçbir zaman bir veritabanında depolanmaması gerekir. Çıktı noktasındaki kodlama, verilerin kullanımını, Örneğin HTML 'den bir sorgu dizesi değerine değiştirmenize izin verir. Ayrıca, aramadan önce değerleri kodlamak zorunda kalmadan verilerinize kolayca arama yapmanızı sağlar ve kodlayıcılara yapılan tüm değişikliklerden veya hata düzeltmelerinden faydalanabilirsiniz.

## <a name="validation-as-an-xss-prevention-technique"></a>XSS önleme tekniği olarak doğrulama

Doğrulama, XSS saldırılarını sınırlayan yararlı bir araç olabilir. Örneğin, yalnızca 0-9 karakterlerini içeren bir sayısal dize XSS saldırısı tetiklemez. Doğrulama, Kullanıcı girişinde HTML kabul edilirken daha karmaşık hale gelir. Mümkün değilse HTML girişinin ayrıştırılması zordur. Gömülü HTML 'yi katmış bir ayrıştırıcıyla bağlanmış markaşağı, zengin girişi kabul etmek için daha güvenli bir seçenektir. Yalnızca doğrulamaya hiçbir şekilde güvenmeyin. Doğrulama veya temizleme işlemi gerçekleştirildiğine bakılmaksızın, çıkış yapmadan önce güvenilmeyen girişi her zaman kodlayın.
