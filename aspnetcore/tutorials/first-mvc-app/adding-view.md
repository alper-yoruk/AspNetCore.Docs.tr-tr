---
title: Bölüm 3, ASP.NET Core MVC uygulamasına bir görünüm ekleme
author: rick-anderson
description: ASP.NET Core MVC 'de öğretici serisinin 3. bölümü.
ms.author: riande
ms.date: 8/04/2019
no-loc:
- appsettings.json
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
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 56f39643ccd69c6508148374033208eb3b2f25ab
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422723"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a>Bölüm 3, ASP.NET Core MVC uygulamasına bir görünüm ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Bu bölümde, `HelloWorldController` [Razor](xref:mvc/views/razor) BIR istemciye HTML yanıtları oluşturma işlemini düzgün bir şekilde kapsüllemek için, sınıfını görünüm dosyalarını kullanacak şekilde değiştirirsiniz.

Kullanarak bir görünüm şablonu dosyası oluşturursunuz Razor . Razortabanlı görünüm şablonlarının *. cshtml* dosya uzantısı vardır. C# ile HTML çıktısı oluşturmanın zarif bir yolunu sağlarlar.

Şu anda `Index` yöntemi, denetleyici sınıfında sabit kodlanmış bir ileti içeren bir dize döndürür. Sınıfında, `HelloWorldController` `Index` yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır. HTML yanıtı oluşturmak için bir görünüm şablonu kullanır. Yukarıdaki yöntem gibi denetleyici Yöntemleri ( *eylem yöntemleri* olarak da bilinir), `Index` genellikle <xref:Microsoft.AspNetCore.Mvc.IActionResult> <xref:Microsoft.AspNetCore.Mvc.ActionResult> gibi bir tür değil, genellikle bir (veya öğesinden türetilmiş bir sınıf) döndürür `string` .

## <a name="add-a-view"></a>Görünüm ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld* klasörünü adlandırın.

* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni öğe ekleyin**.

* **Yeni öğe Ekle-Mvcfilmi** iletişim kutusunda

  * Sağ üst köşedeki arama kutusuna *Görünüm* girin

  * **Razor Görünüm** Seç

  * *Index. cshtml* **adlı ad** kutusu değerini saklayın.

  * **Ekle** ’yi seçin

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

İçin bir `Index` görünüm ekleyin `HelloWorldController` .

* *Views/HelloWorld* adlı yeni bir klasör ekleyin.
* *Views/HelloWorld* klasör adı *Index. cshtml* dosyasına yeni bir dosya ekleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* *Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld* klasörünü adlandırın.
* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni dosya ekleyin**.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **ASP .NET Core** ' u seçin.
  * Orta bölmedeki **MVC görünümü sayfasını** seçin.
  * **Ad** kutusuna *Index* yazın.
  * **Yeni** 'yi seçin.

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view_mac.png)

---

*Views/HelloWorld/Index. cshtml* Razor Görünüm dosyasının içeriğini aşağıdakiler ile değiştirin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

`https://localhost:{PORT}/HelloWorld` sayfasına gidin. `Index`' Deki yöntemi `HelloWorldController` çok fazla değil `return View();` , yönteminin tarayıcıya yanıt işlemek için bir görünüm şablonu dosyası kullanması gerektiğini belirten ifadesini çalıştırdı. Bir görünüm şablonu dosya adı belirtilmediğinden, MVC varsayılan görünüm dosyasını kullanmaya göre varsayılan olarak ayarlanmış. Varsayılan görünüm dosyası yöntemiyle aynı ada sahiptir ( `Index` ), bu nedenle */views/HelloWorld/Index.cshtml* içindeki görünüm şablonu kullanılır. Aşağıdaki görüntüde "görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir görünümde sabit kodlanmış.

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Görünümleri ve düzen sayfalarını değiştirme

Menü bağlantılarını ( **Mvcmovie** , **Home** ve **Gizlilik** ) seçin. Her sayfada aynı menü düzeni gösterilir. Menü düzeni *Görünümler/Shared/_Layout. cshtml* dosyasında uygulanır. *Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın.

[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır. Satırı bulun `@RenderBody()` . `RenderBody` , oluşturduğunuz tüm görünüme özgü sayfaların, Düzen sayfasında *kaydırılan* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Home/privacy. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme

*Görünümler/paylaşılan/_Layout. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin. Değişiklikler vurgulanır:
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Yukarıdaki biçimlendirme aşağıdaki değişiklikleri yaptı:

* ' nin ' `MvcMovie` öğesine tekrarı `Movie App` .
* Öğesine bağla öğesi `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .

Önceki biçimlendirmede, `asp-area=""` Bu uygulama [alan](xref:mvc/controllers/areas)kullandığından [tutturucu etiketi Yardımcısı özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ve öznitelik değeri atlandı.

**Not** : `Movies` Denetleyici uygulanmadı. Bu noktada `Movie App` bağlantı işlevsel değildir.

Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin. Tarayıcı sekmesindeki başlığın Gizlilik ilkesi yerine bir **film uygulaması** (Gizlilik ilkesi değil) nasıl görüntülediğini fark edin **-MVC filmi** :

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

**Giriş** bağlantısını seçin ve başlık ve bağlantı metninin **film uygulamasını** da görüntülediğine dikkat edin. Düzen şablonunda değişikliği bir kez yapabildik ve sitedeki tüm sayfalar yeni bağlantı metnini ve yeni başlığı yansıtmaktadır.

*Views/_ViewStart. cshtml* dosyasını inceleyin:

```cshtml
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart. cshtml* dosyası her bir görünüm için *views/Shared/_Layout. cshtml* dosyasını getirir. `Layout`Özelliği, farklı bir düzen görünümü ayarlamak veya bir `null` Düzen dosyası kullanılmayacak şekilde ayarlamak için kullanılabilir.

`<h2>` *Views/HelloWorld/Index. cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod bitini görüntülemeyi değiştirmekten daha fazla bilgi alabilirsiniz.

`ViewData["Title"] = "Movie List";` Yukarıdaki kodda, `Title` `ViewData` sözlüğün özelliğini "film listesi" olarak ayarlar. `Title`Özelliği, `<title>` Düzen sayfasındaki HTML öğesinde kullanılır:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Değişikliği kaydedin ve öğesine gidin `https://localhost:{PORT}/HelloWorld` . Tarayıcı başlığı, birincil başlık ve ikincil başlıkların değiştirildiğini unutmayın. (Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülüyor olabilirsiniz. Sunucudan gelen yanıtı zorlamak için tarayıcınızda CTRL + F5 tuşlarına basın.) Tarayıcı başlığı `ViewData["Title"]` *Index. cshtml* görünüm şablonunda ve düzen dosyasına eklenen ek "-film uygulaması" olarak ayarlandığımızda oluşturulur.

*Index. cshtml* görünüm şablonundaki içerik *views/Shared/_Layout. cshtml* görünüm şablonuyla birleştirilir. Tarayıcıya tek bir HTML yanıtı gönderilir. Düzen şablonları, bir uygulamadaki tüm sayfalara uygulanan değişiklikler yapmayı kolaylaştırır. Daha fazla bilgi için bkz. [Düzen](xref:mvc/views/layout).

![Film listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

"Data" (Bu durumda "Görünümümüzden Merhaba!") çok az. ileti) sabit kodludur, ancak. MVC uygulamasında bir "V" (görünüm) var ve bir "C" (denetleyici) var, ancak henüz "M" (model) yok.

## <a name="passing-data-from-the-controller-to-the-view"></a>Denetleyiciden görünüme veri geçirme

Gelen URL isteğine yanıt olarak denetleyici eylemleri çağrılır. Bir denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir. Denetleyici verileri bir veri kaynağından alır ve tarayıcıya ne tür bir yanıt gönderileceğini belirler. Görünüm şablonları bir denetleyiciden, tarayıcıya HTML yanıtı oluşturmak ve biçimlendirmek için kullanılabilir.

Bir görünüm şablonunun yanıt işlemesi için gereken verileri sağlamaktan denetleyiciler sorumludur. En iyi yöntem: Görünüm şablonları iş **mantığı gerçekleştirmemelidir** veya doğrudan bir veritabanıyla etkileşime girmemelidir. Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sunulan verilerle birlikte çalışmalıdır. Bu "kaygıları ayrımı", kodun temiz, test edilebilir ve sürdürülebilir kalmasına yardımcı olur.

Şu anda, `Welcome` `HelloWorldController` sınıfındaki yöntemi bir `name` ve parametresi alır `ID` ve sonra değerleri doğrudan tarayıcıya çıkarır. Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, denetleyiciyi bir görünüm şablonu kullanacak şekilde değiştirin. Görünüm şablonu dinamik bir yanıt üretir, bu, yanıtı oluşturmak için denetleyiciden görünüme uygun veri bitlerinin geçirilmesi gereken anlamına gelir. Bu, denetleyicinin görünüm şablonu tarafından daha sonra erişebileceği bir sözlükte görünüm şablonunun gerektirdiği dinamik verileri (parametreler) yerleştirerek bunu yapın `ViewData` .

*HelloWorldController.cs* içinde, `Welcome` sözlüğe bir ve değeri eklemek için yöntemini değiştirin `Message` `NumTimes` `ViewData` . `ViewData`Sözlük dinamik bir nesnedir, yani herhangi bir tür kullanılabilir; `ViewData` nesnenin içine bir öğe yerleştirene kadar tanımlanmış bir özelliği yoktur. [MVC modeli bağlama sistemi](xref:mvc/models/model-binding) , adlandırılmış parametreleri ( `name` ve `numTimes` ) adres çubuğundaki sorgu dizesinden, yöntemizdeki parametrelere otomatik olarak eşler. Tüm *HelloWorldController.cs* dosyası şuna benzer:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData`Sözlük nesnesi görünüme geçirilecek verileri içerir.

*Görünümler/HelloWorld/Welcome. cshtml* adlı bir hoş geldiniz görünüm şablonu oluşturun.

*Welcome. cshtml* görünüm şablonunda "Hello" öğesini görüntüleyen bir döngü oluşturacaksınız `NumTimes` . *Views/HelloWorld/Welcome. cshtml* içeriğini aşağıdakiler ile değiştirin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Değişikliklerinizi kaydedin ve aşağıdaki URL 'ye gidin:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Veriler URL 'den alınır ve [MVC model Bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye geçirilir. Denetleyici, verileri bir `ViewData` Sözlükte paketler ve bu nesneyi görünüme geçirir. Daha sonra Görünüm, verileri tarayıcıda HTML olarak işler.

![Bir hoş geldiniz etiketi ve dört kez gösterilen Hello Rick ifadesi gösteren gizlilik görünümü](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Yukarıdaki örnekte `ViewData` Sözlük, denetleyicideki verileri bir görünüme geçirmek için kullanıldı. Öğreticide daha sonra bir görünüm modeli, bir denetleyicideki verileri bir görünüme geçirmek için kullanılır. Veri geçirme yaklaşımına yönelik görünüm modeli, sözlük yaklaşımına göre genel olarak tercih edilir `ViewData` . Daha fazla bilgi için bkz. [ViewBag, ViewData veya TempData kullanma](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

Sonraki öğreticide, bir film veritabanı oluşturulur.

> [!div class="step-by-step"]
> [Önceki](adding-controller.md) 
>  [Sonraki](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu bölümde, `HelloWorldController` [Razor](xref:mvc/views/razor) BIR istemciye HTML yanıtları oluşturma işlemini düzgün bir şekilde kapsüllemek için, sınıfını görünüm dosyalarını kullanacak şekilde değiştirirsiniz.

Kullanarak bir görünüm şablonu dosyası oluşturursunuz Razor . Razortabanlı görünüm şablonlarının *. cshtml* dosya uzantısı vardır. C# ile HTML çıktısı oluşturmanın zarif bir yolunu sağlarlar.

Şu anda `Index` yöntemi, denetleyici sınıfında sabit kodlanmış bir ileti içeren bir dize döndürür. Sınıfında, `HelloWorldController` `Index` yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır. HTML yanıtı oluşturmak için bir görünüm şablonu kullanır. Yukarıdaki yöntem gibi denetleyici Yöntemleri ( *eylem yöntemleri* olarak da bilinir), `Index` genellikle <xref:Microsoft.AspNetCore.Mvc.IActionResult> <xref:Microsoft.AspNetCore.Mvc.ActionResult> gibi bir tür değil, genellikle bir (veya öğesinden türetilmiş bir sınıf) döndürür `string` .

## <a name="add-a-view"></a>Görünüm ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld* klasörünü adlandırın.

* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni öğe ekleyin**.

* **Yeni öğe Ekle-Mvcfilmi** iletişim kutusunda

  * Sağ üst köşedeki arama kutusuna *Görünüm* girin

  * **Razor Görünüm** Seç

  * *Index. cshtml* **adlı ad** kutusu değerini saklayın.

  * **Ekle** ’yi seçin

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

İçin bir `Index` görünüm ekleyin `HelloWorldController` .

* *Views/HelloWorld* adlı yeni bir klasör ekleyin.
* *Views/HelloWorld* klasör adı *Index. cshtml* dosyasına yeni bir dosya ekleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* *Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld* klasörünü adlandırın.
* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni dosya ekleyin**.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **Web** ' i seçin.
  * Orta bölmedeki **boş HTML dosyasını** seçin.
  * **Ad** kutusuna *Index. cshtml* yazın.
  * **Yeni** 'yi seçin.

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view_mac.png)

---

*Views/HelloWorld/Index. cshtml* Razor Görünüm dosyasının içeriğini aşağıdakiler ile değiştirin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

`https://localhost:{PORT}/HelloWorld` sayfasına gidin. `Index`' Deki yöntemi `HelloWorldController` çok fazla değil `return View();` , yönteminin tarayıcıya yanıt işlemek için bir görünüm şablonu dosyası kullanması gerektiğini belirten ifadesini çalıştırdı. Bir görünüm şablonu dosya adı belirtilmediğinden, MVC varsayılan görünüm dosyasını kullanmaya göre varsayılan olarak ayarlanmış. Varsayılan görünüm dosyası yöntemiyle aynı ada sahiptir ( `Index` ), bu nedenle */views/HelloWorld/Index.cshtml* kullanılır. Aşağıdaki görüntüde "görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir görünümde sabit kodlanmış.

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Görünümleri ve düzen sayfalarını değiştirme

Menü bağlantılarını ( **Mvcmovie** , **Home** ve **Gizlilik** ) seçin. Her sayfada aynı menü düzeni gösterilir. Menü düzeni *Görünümler/Shared/_Layout. cshtml* dosyasında uygulanır. *Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın.

[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır. Satırı bulun `@RenderBody()` . `RenderBody` , oluşturduğunuz tüm görünüme özgü sayfaların, Düzen sayfasında *kaydırılan* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Home/privacy. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme

* Başlık ve altbilgi öğelerinde öğesini `MvcMovie` olarak değiştirin `Movie App` .
* Tutturucu öğesini olarak değiştirin `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .

Aşağıdaki biçimlendirme vurgulanan değişiklikleri göstermektedir:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

Önceki biçimlendirmede, `asp-area` Bu uygulama [alan](xref:mvc/controllers/areas)kullandığından [tutturucu etiketi yardımcı özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) atlandı.

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Not** : `Movies` Denetleyici uygulanmadı. Bu noktada `Movie App` bağlantı işlevsel değildir.

Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin. Tarayıcı sekmesindeki başlığın Gizlilik ilkesi yerine bir **film uygulaması** (Gizlilik ilkesi değil) nasıl görüntülediğini fark edin **-MVC filmi** :

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

**Giriş** bağlantısını seçin ve başlık ve bağlantı metninin **film uygulamasını** da görüntülediğine dikkat edin. Düzen şablonunda değişikliği bir kez yapabildik ve sitedeki tüm sayfalar yeni bağlantı metnini ve yeni başlığı yansıtmaktadır.

*Views/_ViewStart. cshtml* dosyasını inceleyin:

```cshtml
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart. cshtml* dosyası her bir görünüm için *views/Shared/_Layout. cshtml* dosyasını getirir. `Layout`Özelliği, farklı bir düzen görünümü ayarlamak veya bir `null` Düzen dosyası kullanılmayacak şekilde ayarlamak için kullanılabilir.

`<h2>` *Views/HelloWorld/Index. cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod bitini görüntülemeyi değiştirmekten daha fazla bilgi alabilirsiniz.

`ViewData["Title"] = "Movie List";` Yukarıdaki kodda, `Title` `ViewData` sözlüğün özelliğini "film listesi" olarak ayarlar. `Title`Özelliği, `<title>` Düzen sayfasındaki HTML öğesinde kullanılır:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Değişikliği kaydedin ve öğesine gidin `https://localhost:{PORT}/HelloWorld` . Tarayıcı başlığı, birincil başlık ve ikincil başlıkların değiştirildiğini unutmayın. (Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülüyor olabilirsiniz. Sunucudan gelen yanıtı zorlamak için tarayıcınızda CTRL + F5 tuşlarına basın.) Tarayıcı başlığı `ViewData["Title"]` *Index. cshtml* görünüm şablonunda ve düzen dosyasına eklenen ek "-film uygulaması" olarak ayarlandığımızda oluşturulur.

Ayrıca, *Index. cshtml* görünüm şablonundaki içeriğin *Görünümler/paylaşılan/_Layout. cshtml* görünüm şablonuyla nasıl birleştirildiğini ve tarayıcıya tek bir HTML yanıtı gönderildiğini de unutmayın. Düzen şablonları, uygulamanızdaki tüm sayfalara uygulanan değişiklikler yapmayı gerçekten kolaylaştırır. Daha fazla bilgi için bkz. [Düzen](xref:mvc/views/layout).

![Film listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

"Data" (Bu durumda "Görünümümüzden Merhaba!") çok az. ileti) sabit kodludur, ancak. MVC uygulamasında bir "V" (görünüm) var ve bir "C" (denetleyici) var, ancak henüz "M" (model) yok.

## <a name="passing-data-from-the-controller-to-the-view"></a>Denetleyiciden görünüme veri geçirme

Gelen URL isteğine yanıt olarak denetleyici eylemleri çağrılır. Bir denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir. Denetleyici verileri bir veri kaynağından alır ve tarayıcıya ne tür bir yanıt gönderileceğini belirler. Görünüm şablonları bir denetleyiciden, tarayıcıya HTML yanıtı oluşturmak ve biçimlendirmek için kullanılabilir.

Bir görünüm şablonunun yanıt işlemesi için gereken verileri sağlamaktan denetleyiciler sorumludur. En iyi yöntem: Görünüm şablonları iş **mantığı gerçekleştirmemelidir** veya doğrudan bir veritabanıyla etkileşime girmemelidir. Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sunulan verilerle birlikte çalışmalıdır. Bu "kaygıları ayrımı", kodun temiz, test edilebilir ve sürdürülebilir kalmasına yardımcı olur.

Şu anda, `Welcome` `HelloWorldController` sınıfındaki yöntemi bir `name` ve parametresi alır `ID` ve sonra değerleri doğrudan tarayıcıya çıkarır. Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, denetleyiciyi bir görünüm şablonu kullanacak şekilde değiştirin. Görünüm şablonu dinamik bir yanıt üretir, bu, yanıtı oluşturmak için denetleyiciden görünüme uygun veri bitlerinin geçirilmesi gereken anlamına gelir. Bu, denetleyicinin görünüm şablonu tarafından daha sonra erişebileceği bir sözlükte görünüm şablonunun gerektirdiği dinamik verileri (parametreler) yerleştirerek bunu yapın `ViewData` .

*HelloWorldController.cs* içinde, `Welcome` sözlüğe bir ve değeri eklemek için yöntemini değiştirin `Message` `NumTimes` `ViewData` . `ViewData`Sözlük dinamik bir nesnedir, yani herhangi bir tür kullanılabilir; `ViewData` nesnenin içine bir öğe yerleştirene kadar tanımlanmış bir özelliği yoktur. [MVC modeli bağlama sistemi](xref:mvc/models/model-binding) , adlandırılmış parametreleri ( `name` ve `numTimes` ) adres çubuğundaki sorgu dizesinden, yöntemizdeki parametrelere otomatik olarak eşler. Tüm *HelloWorldController.cs* dosyası şuna benzer:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData`Sözlük nesnesi görünüme geçirilecek verileri içerir.

*Görünümler/HelloWorld/Welcome. cshtml* adlı bir hoş geldiniz görünüm şablonu oluşturun.

*Welcome. cshtml* görünüm şablonunda "Hello" öğesini görüntüleyen bir döngü oluşturacaksınız `NumTimes` . *Views/HelloWorld/Welcome. cshtml* içeriğini aşağıdakiler ile değiştirin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Değişikliklerinizi kaydedin ve aşağıdaki URL 'ye gidin:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Veriler URL 'den alınır ve [MVC model Bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye geçirilir. Denetleyici, verileri bir `ViewData` Sözlükte paketler ve bu nesneyi görünüme geçirir. Daha sonra Görünüm, verileri tarayıcıda HTML olarak işler.

![Bir hoş geldiniz etiketi ve dört kez gösterilen Hello Rick ifadesi gösteren gizlilik görünümü](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Yukarıdaki örnekte `ViewData` Sözlük, denetleyicideki verileri bir görünüme geçirmek için kullanıldı. Öğreticide daha sonra bir görünüm modeli, bir denetleyicideki verileri bir görünüme geçirmek için kullanılır. Veri geçirme yaklaşımına yönelik görünüm modeli, sözlük yaklaşımına göre genel olarak tercih edilir `ViewData` . Daha fazla bilgi için bkz. [ViewBag, ViewData veya TempData kullanma](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

Sonraki öğreticide, bir film veritabanı oluşturulur.

> [!div class="step-by-step"]
> [Önceki](adding-controller.md) 
>  [Sonraki](adding-model.md)

::: moniker-end
