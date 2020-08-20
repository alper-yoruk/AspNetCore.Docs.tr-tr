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
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="a1fad-103">ASP.NET Core siteler arası komut dosyası (XSS) engelle</span><span class="sxs-lookup"><span data-stu-id="a1fad-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="a1fad-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a1fad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a1fad-105">Siteler arası betik oluşturma (XSS), bir saldırganın istemci tarafı komut dosyalarını (genellikle JavaScript) Web sayfalarına yerleştirmesini sağlayan bir güvenlik açığıdır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="a1fad-106">Diğer kullanıcılar etkilenen sayfaları yüklediğine göre, saldırganın betikleri çalışacaktır ve saldırganın, oturum belirteçleri çalıştırmasına, cookie Web sayfasının IÇERIĞINI Dom düzenlemesi aracılığıyla değiştirmesine veya tarayıcıyı başka bir sayfaya yönlendirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="a1fad-107">XSS Güvenlik Açıkları genellikle bir uygulama kullanıcı girişi aldığında ve bunu doğrulamadan, kodlamadan veya çıkarmadan bir sayfaya çıktılarken oluşur.</span><span class="sxs-lookup"><span data-stu-id="a1fad-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="a1fad-108">Uygulamanızı XSS 'ye karşı koruma</span><span class="sxs-lookup"><span data-stu-id="a1fad-108">Protecting your application against XSS</span></span>

<span data-ttu-id="a1fad-109">Temel düzeyde XSS `<script>` , uygulamanıza, işlenmiş sayfanıza bir etiket ekleyerek veya bir olayı bir olaya ekleyerek çalışarak işe yarar `On*` .</span><span class="sxs-lookup"><span data-stu-id="a1fad-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="a1fad-110">Geliştiriciler, uygulamaları için XSS 'ye giriş yapmaktan kaçınmak için aşağıdaki önleme adımlarını kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="a1fad-111">Aşağıdaki adımları takip etmediğiniz takdirde, güvenilmeyen verileri hiçbir şekilde HTML girişine yerleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="a1fad-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="a1fad-112">Güvenilmeyen veriler, bir saldırgan olarak bir saldırgan, HTML form girişleri, sorgu dizeleri, HTTP üst bilgileri ve bir saldırgan tarafından denetlenebilecek veri kaynağı, uygulamanızı ihlal edemese bile veritabanınızı ihlal edebiliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="a1fad-113">Güvenli olmayan verileri bir HTML öğesine geçirmeden önce, HTML kodlamalı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="a1fad-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="a1fad-114">HTML kodlaması gibi karakterler alır &lt; ve lt; gibi güvenli bir biçimde değiştirir &amp;</span><span class="sxs-lookup"><span data-stu-id="a1fad-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="a1fad-115">Güvenilir olmayan verileri HTML özniteliğine geçirmeden önce, HTML kodlamalı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="a1fad-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="a1fad-116">HTML öznitelik kodlaması, HTML kodlamasının bir üst kümesidir ve "ve" gibi ek karakterlerin kodlamasını belirler.</span><span class="sxs-lookup"><span data-stu-id="a1fad-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="a1fad-117">Güvenilir olmayan verileri JavaScript 'e geçirmeden önce, verileri çalışma zamanında aldığınız bir HTML öğesine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="a1fad-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="a1fad-118">Bu mümkün değilse, verilerin JavaScript kodlamalı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="a1fad-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="a1fad-119">JavaScript kodlaması, JavaScript için tehlikeli karakterler alır ve bunları onaltılı olarak değiştirir. Örneğin, &lt; olarak kodlanır `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="a1fad-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="a1fad-120">Güvenilmeyen verileri bir URL sorgu dizesine geçirmeden önce URL 'nin kodlandığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="a1fad-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="a1fad-121">Kullanarak HTML kodlaması Razor</span><span class="sxs-lookup"><span data-stu-id="a1fad-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="a1fad-122">RazorMVC 'de kullanılan motor, bunu yapmamaya gerçekten engel olmadığınız müddetçe, değişkenlerden kaynaklanan tüm çıktıyı otomatik olarak kodlar.</span><span class="sxs-lookup"><span data-stu-id="a1fad-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="a1fad-123">Yönergesini her kullandığınızda HTML öznitelik kodlama kurallarını kullanır *@* .</span><span class="sxs-lookup"><span data-stu-id="a1fad-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="a1fad-124">Html öznitelik kodlaması bir HTML kodlaması üst kümesidir, bu, HTML kodlaması veya HTML öznitelik kodlaması kullanılması gerekip gerekmediği için kendinize sorun olması gerekmediği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="a1fad-125">Güvenilmeyen girişi doğrudan JavaScript 'e eklemeye çalışırken değil, yalnızca bir HTML içeriğinde @ kullandığınızdan emin olmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="a1fad-126">Etiket Yardımcıları, etiket parametrelerinde kullandığınız girişi de kodlayacaktır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="a1fad-127">Aşağıdaki görünümü uygulayın Razor :</span><span class="sxs-lookup"><span data-stu-id="a1fad-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="a1fad-128">Bu görünüm *Untrustedınput* değişkeninin içeriğini verir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="a1fad-129">Bu değişken, XSS saldırılarında kullanılan bazı karakterleri içerir, yani &lt; "ve &gt; .</span><span class="sxs-lookup"><span data-stu-id="a1fad-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="a1fad-130">Kaynağı incelemek, işlenen çıktının şu şekilde kodlanmış olduğunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="a1fad-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="a1fad-131">ASP.NET Core MVC, `HtmlString` Çıkış üzerine otomatik olarak kodlanmayan bir sınıf sağlar.</span><span class="sxs-lookup"><span data-stu-id="a1fad-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="a1fad-132">Bu, XSS Güvenlik Açığı sunan güvenilir olmayan girişle birlikte asla kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="a1fad-133">Kullanılarak JavaScript kodlaması Razor</span><span class="sxs-lookup"><span data-stu-id="a1fad-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="a1fad-134">Görünümünüzde işlemek için JavaScript 'e bir değer eklemek istediğiniz zaman olabilir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="a1fad-135">Bunu yapmanın iki yolu vardır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-135">There are two ways to do this.</span></span> <span data-ttu-id="a1fad-136">Değer eklemenin en güvenli yolu, değeri bir etiketinin veri özniteliğinde yerleştirmenin ve JavaScript 'te alamaktır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="a1fad-137">Örnek:</span><span class="sxs-lookup"><span data-stu-id="a1fad-137">For example:</span></span>

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

<span data-ttu-id="a1fad-138">Bu, aşağıdaki HTML 'yi oluşturur</span><span class="sxs-lookup"><span data-stu-id="a1fad-138">This will produce the following HTML</span></span>

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

<span data-ttu-id="a1fad-139">Bu işlem çalıştığında, aşağıdakileri işleyecek:</span><span class="sxs-lookup"><span data-stu-id="a1fad-139">Which, when it runs, will render the following:</span></span>

```
<"123">
   <"123">
```

<span data-ttu-id="a1fad-140">JavaScript Encoder 'ı doğrudan de çağırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a1fad-140">You can also call the JavaScript encoder directly:</span></span>

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

<span data-ttu-id="a1fad-141">Bu, tarayıcıda şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="a1fad-141">This will render in the browser as follows:</span></span>

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> <span data-ttu-id="a1fad-142">DOM öğeleri oluşturmak için, JavaScript 'te güvenilmeyen girişi birleştirme.</span><span class="sxs-lookup"><span data-stu-id="a1fad-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="a1fad-143">Ya da gibi `createElement()` özellik değerlerini kullanmanız ve atamanız `node.TextContent=` ya da başka bir şekilde `element.SetAttribute()` / `element[attribute]=` kendi kendinize Dom tabanlı XSS 'de kullanıma sunacaksınız.</span><span class="sxs-lookup"><span data-stu-id="a1fad-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="a1fad-144">Koddaki kodlayıcılara erişme</span><span class="sxs-lookup"><span data-stu-id="a1fad-144">Accessing encoders in code</span></span>

<span data-ttu-id="a1fad-145">HTML, JavaScript ve URL kodlayıcıları kodunuzda iki şekilde kullanılabilir, bunları [bağımlılık ekleme](xref:fundamentals/dependency-injection) yoluyla ekleyebilir veya ad alanında bulunan varsayılan kodlayıcıları kullanabilirsiniz `System.Text.Encodings.Web` .</span><span class="sxs-lookup"><span data-stu-id="a1fad-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="a1fad-146">Varsayılan kodlayıcıları kullanırsanız, güvenli olarak değerlendirilecek karakter aralıklarına uyguladığınız her türlü, varsayılan kodlayıcılar mümkün olan en güvenli kodlama kurallarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="a1fad-147">Yapılandırıcılar aracılığıyla yapılandırılabilir kodlayıcıları kullanmak için, kurucularınız uygun şekilde bir *Htmlencoder*, *javascriptencoder* ve *URLEncoder* parametresi almalıdır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="a1fad-148">Örneğin;</span><span class="sxs-lookup"><span data-stu-id="a1fad-148">For example;</span></span>

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

## <a name="encoding-url-parameters"></a><span data-ttu-id="a1fad-149">Kodlama URL parametreleri</span><span class="sxs-lookup"><span data-stu-id="a1fad-149">Encoding URL Parameters</span></span>

<span data-ttu-id="a1fad-150">Değer olarak güvenilmeyen giriş içeren bir URL sorgu dizesi derlemek istiyorsanız, `UrlEncoder` değeri kodlamak için öğesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="a1fad-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="a1fad-151">Örneğin,</span><span class="sxs-lookup"><span data-stu-id="a1fad-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="a1fad-152">Kodladıktan sonra, encodedValue değişkeni de içerecektir `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="a1fad-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="a1fad-153">Boşluk, tırnak, noktalama ve diğer güvenli olmayan karakterler, onaltılık değerlerine göre yüzde olarak kodlanır, örneğin bir boşluk karakteri %20 olur.</span><span class="sxs-lookup"><span data-stu-id="a1fad-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="a1fad-154">Güvenilmeyen girişi, URL yolunun bir parçası olarak kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="a1fad-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="a1fad-155">Güvenilmeyen girişi her zaman bir sorgu dizesi değeri olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="a1fad-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="a1fad-156">Kodlayıcıları özelleştirme</span><span class="sxs-lookup"><span data-stu-id="a1fad-156">Customizing the Encoders</span></span>

<span data-ttu-id="a1fad-157">Varsayılan olarak kodlayıcılar, temel Latin Unicode aralığıyla sınırlı bir güvenli liste kullanır ve karakter kodu eşdeğerleri olarak bu aralığın dışındaki tüm karakterleri kodlayabilir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="a1fad-158">Bu davranış Razor , Dizelerinizin çıktısını almak için kodlayıcıları kullanacağı için taghelper ve HtmlHelper işlemesini da etkiler.</span><span class="sxs-lookup"><span data-stu-id="a1fad-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="a1fad-159">Bunun arkasındaki nedenler bilinmeyen veya gelecekteki tarayıcı hataları karşısında korunmaktan (önceki tarayıcı hataları Ingilizce olmayan karakterlerin işlenmesine göre ayrıştırılır).</span><span class="sxs-lookup"><span data-stu-id="a1fad-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="a1fad-160">Web siteniz Çince, Kiril veya diğerleri gibi Latin olmayan karakterlerin büyük bir bölümünü kullanıyorsa, bu durum muhtemelen istediğiniz davranış değildir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="a1fad-161">' De başlangıç sırasında uygulamanıza uygun Unicode aralıklarını dahil etmek için Kodlayıcı güvenli listelerini özelleştirebilirsiniz `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="a1fad-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="a1fad-162">Örneğin, varsayılan yapılandırmayı kullanarak, Razor benzer bir HtmlHelper kullanabilirsiniz;</span><span class="sxs-lookup"><span data-stu-id="a1fad-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="a1fad-163">Web sayfasının kaynağını görüntülediğinizde, Çince metin kodlamalı şekilde, aşağıdaki şekilde işlenmiş olduğunu görürsünüz;</span><span class="sxs-lookup"><span data-stu-id="a1fad-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="a1fad-164">Kodlayıcı tarafından güvenli olarak değerlendirilen karakterleri genişletmek için, içindeki yöntemine aşağıdaki satırı ekleyin `ConfigureServices()` `startup.cs` ;</span><span class="sxs-lookup"><span data-stu-id="a1fad-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="a1fad-165">Bu örnek, Cjkunifiedideograf Unicode aralığını dahil etmek için güvenli listeyi widens.</span><span class="sxs-lookup"><span data-stu-id="a1fad-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="a1fad-166">Oluşturulan çıkış şimdi şu şekilde olur</span><span class="sxs-lookup"><span data-stu-id="a1fad-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="a1fad-167">Güvenli liste aralıkları, diller değil Unicode kod grafikleri olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="a1fad-168">[Unicode standardında](https://unicode.org/) , karakterlerinizi içeren grafiği bulmak için kullanabileceğiniz bir [kod grafikleri](https://www.unicode.org/charts/index.html) listesi bulunur.</span><span class="sxs-lookup"><span data-stu-id="a1fad-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="a1fad-169">Her kodlayıcı, HTML, JavaScript ve URL 'Nin ayrı ayrı yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="a1fad-170">Güvenli listenin özelleştirilmesi yalnızca DI aracılığıyla kaynak kodlayıcıları etkiler.</span><span class="sxs-lookup"><span data-stu-id="a1fad-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="a1fad-171">Daha sonra varsayılan olarak bir kodlayıcıyla doğrudan erişmeniz durumunda `System.Text.Encodings.Web.*Encoder.Default` yalnızca temel Latin SafeList kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="a1fad-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="a1fad-172">Kodlama nerede gerçekleşmelidir?</span><span class="sxs-lookup"><span data-stu-id="a1fad-172">Where should encoding take place?</span></span>

<span data-ttu-id="a1fad-173">Genel kabul edilen uygulama, kodlamanın çıkış noktasında gerçekleşmesi ve kodlanmış değerlerin hiçbir zaman bir veritabanında depolanmaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="a1fad-174">Çıktı noktasındaki kodlama, verilerin kullanımını, Örneğin HTML 'den bir sorgu dizesi değerine değiştirmenize izin verir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="a1fad-175">Ayrıca, aramadan önce değerleri kodlamak zorunda kalmadan verilerinize kolayca arama yapmanızı sağlar ve kodlayıcılara yapılan tüm değişikliklerden veya hata düzeltmelerinden faydalanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a1fad-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="a1fad-176">XSS önleme tekniği olarak doğrulama</span><span class="sxs-lookup"><span data-stu-id="a1fad-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="a1fad-177">Doğrulama, XSS saldırılarını sınırlayan yararlı bir araç olabilir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="a1fad-178">Örneğin, yalnızca 0-9 karakterlerini içeren bir sayısal dize XSS saldırısı tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="a1fad-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="a1fad-179">Doğrulama, Kullanıcı girişinde HTML kabul edilirken daha karmaşık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="a1fad-180">Mümkün değilse HTML girişinin ayrıştırılması zordur.</span><span class="sxs-lookup"><span data-stu-id="a1fad-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="a1fad-181">Gömülü HTML 'yi katmış bir ayrıştırıcıyla bağlanmış markaşağı, zengin girişi kabul etmek için daha güvenli bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="a1fad-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="a1fad-182">Yalnızca doğrulamaya hiçbir şekilde güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="a1fad-182">Never rely on validation alone.</span></span> <span data-ttu-id="a1fad-183">Doğrulama veya temizleme işlemi gerçekleştirildiğine bakılmaksızın, çıkış yapmadan önce güvenilmeyen girişi her zaman kodlayın.</span><span class="sxs-lookup"><span data-stu-id="a1fad-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
