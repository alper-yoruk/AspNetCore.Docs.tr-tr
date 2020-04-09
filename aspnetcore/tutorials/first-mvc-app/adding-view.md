---
title: ASP.NET Core MVC uygulamasına görünüm ekleme
author: rick-anderson
description: Basit bir ASP.NET Core MVC uygulamasına görünüm ekleme
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660211"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC uygulamasına görünüm ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Bu bölümde, istemciye HTML yanıtları oluşturma işlemini temiz bir şekilde kapsüllemek için sınıfı `HelloWorldController` [Razor](xref:mvc/views/razor) görünüm dosyalarını kullanacak şekilde değiştirirsiniz.

Razor kullanarak bir görünüm şablon dosyası oluşturursunuz. Jilet tabanlı görünüm şablonlarında *.cshtml* dosya uzantısı vardır. C# ile HTML çıktısı oluşturmak için zarif bir yol sağlarlar.

Şu `Index` anda yöntem denetleyici sınıfında sabit kodlanmış bir ileti ile bir dize döndürür. `HelloWorldController` Sınıfta, `Index` yöntemi aşağıdaki kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır. HTML yanıtı oluşturmak için bir görünüm şablonu kullanır. Denetleyici yöntemleri *(eylem yöntemleri*olarak da `Index` bilinir), yukarıdaki yöntem <xref:Microsoft.AspNetCore.Mvc.IActionResult> gibi, genellikle <xref:Microsoft.AspNetCore.Mvc.ActionResult>bir (veya `string`türetilen bir sınıf), gibi bir tür değil döndürür .

## <a name="add-a-view"></a>Görünüm ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.

* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **yeni > öğe ekleyin.**

* Yeni **Öğe Ekle - MvcMovie** iletişim kutusunda

  * Sağ üstteki arama kutusuna, *görünüm* girin

  * **Jilet Görünümü'nü** seçin

  * **Ad** kutusu değerini tutun, *Index.cshtml*.

  * **Ekle**’yi seçin

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Için `Index` bir görünüm `HelloWorldController`ekleyin.

* *Görünümler/HelloWorld*adlı yeni bir klasör ekleyin.
* *Görünümler/HelloWorld* klasör adı *Index.cshtml'e*yeni bir dosya ekleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* *Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.
* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **Yeni > Dosya Ekle'** ye tıklayın.
* Yeni **Dosya** iletişim kutusunda:

  * Sol bölmede **ASP .NET Core'u** seçin.
  * Orta bölmede **MVC Görünüm Sayfası'nı** seçin.
  * **Ad** kutusuna *Dizin* yazın.
  * **Yeni'yi**seçin.

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view_mac.png)

---

*Görünümler/HelloWorld/Index.cshtml* Razor görünüm dosyasının içeriğini aşağıdakilerle değiştirin:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

`https://localhost:{PORT}/HelloWorld` sayfasına gidin. Yöntem `Index` çok `HelloWorldController` yapmadı; bu yöntem `return View();`tarayıcıya bir yanıt işlemek için bir görünüm şablondosyası kullanması gerektiğini belirten deyimi koştu. Görünüm şablonu dosya adı belirtilmedi, MVC varsayılan görünüm dosyasını kullanmaya varsayılan olarak. Varsayılan görünüm dosyası yöntemle aynı ada sahiptir (`Index`), bu nedenle */Views/HelloWorld/Index.cshtml* kullanılır. Aşağıdaki resimde "Görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir. görünümünde sabit kodlanmış.

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Görünümleri ve düzen sayfalarını değiştirme

Menü bağlantılarını seçin **(MvcMovie**, **Home**, ve **Privacy**). Her sayfa aynı menü düzenini gösterir. Menü düzeni *Görünümler/Paylaşılan/_Layout.cshtml* dosyasında uygulanır. *Görünümler/Paylaşılan/_Layout.cshtml* dosyasını açın.

[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı düzenini tek bir yerde belirtmenize ve ardından sitenizdeki birden çok sayfaya uygulamanıza olanak tanır. `@RenderBody()` Hattı bul. `RenderBody`oluşturduğunuz tüm görünüme özel sayfaların düzen sayfasına sarılmış olarak *görüntülendiği* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Ev/Gizlilik.cshtml** görünümü `RenderBody` yöntemin içinde işlenir.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme

*Görünümler/Paylaşılan/_Layout.cshtml* dosyasının içeriğini aşağıdaki biçimlendirmeyle değiştirin. Değişiklikler vurgulanır:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

Önceki biçimlendirme aşağıdaki değişiklikleri yaptı:

* 3 oluşumları `MvcMovie` `Movie App`için .
* Bağlantı `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`elemanı.

Önceki biçimlendirmede, `asp-area=""` bu uygulama [Alanlar'ı](xref:mvc/controllers/areas)kullanmadığından, bağlantı noktası [Tag Helper özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ve öznitelik değeri atlandı.

**Not**: `Movies` Denetleyici uygulanmadı. Bu noktada, `Movie App` bağlantı işlevsel değildir.

Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin. Tarayıcı sekmesindeki başlığın **Gizlilik Politikası 'nı** nasıl görüntülenebilene dikkat edin - Gizlilik Politikası yerine Film Uygulaması **- Mvc Movie**:

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Ana **Sayfa** bağlantısını seçin ve başlık ve bağlantı metninin de **Film Uygulamasını**görüntülediğini fark edin. Biz düzen şablonu bir kez değişiklik yapmak ve sitedeki tüm sayfaları yeni bağlantı metni ve yeni başlık yansıtacak başardık.

*Görünümler/_ViewStart.cshtml* dosyasını inceleyin:

```cshtml
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* dosyası, her görünüme *Görünümler/Paylaşılan/_Layout.cshtml* dosyasını getirir. Özellik, `Layout` farklı bir düzen görünümü ayarlamak veya hiçbir `null` düzen dosyasının kullanılmaması için ayarlamak için kullanılabilir.

`<h2>` *Görünümler/HelloWorld/Index.cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod parçasının ekranı değiştirdiğini görebilirsiniz.

`ViewData["Title"] = "Movie List";`yukarıdaki kodda sözlüğün `Title` `ViewData` özelliğini "Film Listesi" olarak ayarlar. Özellik, `Title` düzen sayfasındaki `<title>` HTML öğesinde kullanılır:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Değişikliği kaydedin `https://localhost:{PORT}/HelloWorld`ve ''ye gidin. Tarayıcı başlığının, birincil başlığın ve ikincil başlıkların değiştiğine dikkat edin. (Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülenmiş olabilirsiniz. Sunucudan gelen yanıtı yüklemeye zorlamak için tarayıcınızda Ctrl+F5 tuşuna basın.) Tarayıcı `ViewData["Title"]` *başlığı, Index.cshtml* görünüm şablonuna ve düzen dosyasına eklenen ek "- Film Uygulaması" ile oluşturulur.

*Index.cshtml* görünüm şablonundaki içerik *Görünümler/Paylaşılan/_Layout.cshtml* görünüm şablonuyla birleştirilir. Tarayıcıya tek bir HTML yanıtı gönderilir. Düzen şablonları, bir uygulamadaki tüm sayfalarda geçerli olan değişiklikleri yapmayı kolaylaştırır. Daha fazla bilgi için [Düzen'e](xref:mvc/views/layout)bakın.

![Film Listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Bizim küçük "veri" (bu durumda "Merhaba Bizim Görünüm Şablonu!" mesaj) olsa da, sabit kodlanmış. MVC uygulaması "V" (görünüm) ve bir "C" (denetleyici) var, ama hiçbir "M" (model) henüz.

## <a name="passing-data-from-the-controller-to-the-view"></a>Denetleyiciden Görünüme Veri Aktarma

Denetleyici eylemleri, gelen bir URL isteğine yanıt olarak çağrılır. Denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir. Denetleyici bir veri kaynağından veri alır ve tarayıcıya geri göndermek için ne tür bir yanıt karar verir. Görünüm şablonları tarayıcıya bir HTML yanıtı oluşturmak ve biçimlendirmek için bir denetleyiciden kullanılabilir.

Denetleyiciler, bir görünüm şablonuna yanıt verebilmesi için gereken verileri sağlamakla yükümlüdür. En iyi yöntem: Görünüm şablonları iş mantığı **gerçekleştirmemeli** veya doğrudan bir veritabanıyla etkileşime girmemelidir. Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sağlanan verilerle çalışmalıdır. Bu "endişeleri ayırma" bakımı, kodun temiz, sınanabilir ve korunabilir tutulmasına yardımcı olur.

Şu `Welcome` anda, `HelloWorldController` sınıftaki `name` yöntem `ID` bir ve bir parametre alır ve sonra değerleri doğrudan tarayıcıya çıkarır. Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, bunun yerine görünüm şablonu kullanmak için denetleyiciyi değiştirin. Görünüm şablonu dinamik bir yanıt oluşturur, bu da yanıtı oluşturmak için uygun veri bitlerinin denetleyiciden görünüme geçirilmesi gerektiği anlamına gelir. Denetleyicinin görünüm şablonuna ihtiyaç duyduğu dinamik verileri (parametreleri) görünüm şablonuna erişebileceği bir `ViewData` sözlükte koyarak bunu yapın.

*HelloWorldController.cs,* sözlük `Welcome` için bir `Message` ve `NumTimes` değer `ViewData` eklemek için yöntemi değiştirin. Sözlük `ViewData` dinamik bir nesnedir, bu da herhangi bir türün kullanılabildiği anlamına gelir; nesnenin içine bir şey koyana `ViewData` kadar tanımlı özellikleri yoktur. [MVC model bağlama sistemi,](xref:mvc/models/model-binding) adres çubuğundaki `numTimes`sorgu dizesinden adı geçen parametreleri (ve)`name` yönteminizdeki parametrelere otomatik olarak eşler. Tam *HelloWorldController.cs* dosyası aşağıdaki gibi görünür:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Sözlük `ViewData` nesnesi görünüme geçirilecek verileri içerir.

*Görünümler/HelloWorld/Welcome.cshtml*adlı hoş geldiniz görünümü şablonu oluşturun.

*Welcome.cshtml* görünüm şablonunda "Merhaba" `NumTimes`görüntülenen bir döngü oluşturursunuz. *Views/HelloWorld/Welcome.cshtml* içeriğini aşağıdakilerle değiştirin:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Değişikliklerinizi kaydedin ve aşağıdaki URL'ye göz atın:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Veriler URL'den alınır ve [MVC model bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye aktarılır. Denetleyici verileri bir `ViewData` sözlük tesidikkate alır ve bu nesneyi görünüme geçirir. Görünüm daha sonra verileri tarayıcıya HTML olarak işler.

![Hoş geldiniz etiketini ve Hello Rick ifadesini gösteren gizlilik görünümü dört kez gösterilir](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Yukarıdaki örnekte, `ViewData` sözlük denetleyiciden bir görünüme veri aktarmak için kullanılmıştır. Daha sonra öğreticide, bir görünüm modeli denetleyiciden görünüme veri aktarmak için kullanılır. Veri aktarılabilmek için görünüm modeli yaklaşımı `ViewData` genellikle sözlük yaklaşımına göre daha çok tercih edilir. Daha fazla bilgi [için ViewBag, ViewData veya TempData'nın ne zaman kullanılacağına](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) bakın.

Bir sonraki öğreticide, bir film veritabanı oluşturulur.

> [!div class="step-by-step"]
> [Önceki](adding-controller.md)
> [Sonraki](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu bölümde, istemciye HTML yanıtları oluşturma işlemini temiz bir şekilde kapsüllemek için sınıfı `HelloWorldController` [Razor](xref:mvc/views/razor) görünüm dosyalarını kullanacak şekilde değiştirirsiniz.

Razor kullanarak bir görünüm şablon dosyası oluşturursunuz. Jilet tabanlı görünüm şablonlarında *.cshtml* dosya uzantısı vardır. C# ile HTML çıktısı oluşturmak için zarif bir yol sağlarlar.

Şu `Index` anda yöntem denetleyici sınıfında sabit kodlanmış bir ileti ile bir dize döndürür. `HelloWorldController` Sınıfta, `Index` yöntemi aşağıdaki kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Önceki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır. HTML yanıtı oluşturmak için bir görünüm şablonu kullanır. Denetleyici yöntemleri *(eylem yöntemleri*olarak da `Index` bilinir), yukarıdaki yöntem <xref:Microsoft.AspNetCore.Mvc.IActionResult> gibi, genellikle <xref:Microsoft.AspNetCore.Mvc.ActionResult>bir (veya `string`türetilen bir sınıf), gibi bir tür değil döndürür .

## <a name="add-a-view"></a>Görünüm ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.

* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **yeni > öğe ekleyin.**

* Yeni **Öğe Ekle - MvcMovie** iletişim kutusunda

  * Sağ üstteki arama kutusuna, *görünüm* girin

  * **Jilet Görünümü'nü** seçin

  * **Ad** kutusu değerini tutun, *Index.cshtml*.

  * **Ekle**’yi seçin

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Için `Index` bir görünüm `HelloWorldController`ekleyin.

* *Görünümler/HelloWorld*adlı yeni bir klasör ekleyin.
* *Görünümler/HelloWorld* klasör adı *Index.cshtml'e*yeni bir dosya ekleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* *Görünümler* klasörüne sağ tıklayın ve ardından **> Yeni Klasör ekleyin** ve *HelloWorld*klasörüne isim ver.
* *Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **Yeni > Dosya Ekle'** ye tıklayın.
* Yeni **Dosya** iletişim kutusunda:

  * Sol bölmede **Web'i** seçin.
  * Orta bölmede **Boş HTML dosyasını** seçin.
  * **Ad** kutusuna *Index.cshtml* yazın.
  * **Yeni'yi**seçin.

![Yeni Öğe iletişim kutusu ekle](adding-view/_static/add_view_mac.png)

---

*Görünümler/HelloWorld/Index.cshtml* Razor görünüm dosyasının içeriğini aşağıdakilerle değiştirin:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

`https://localhost:{PORT}/HelloWorld` sayfasına gidin. Yöntem `Index` çok `HelloWorldController` yapmadı; bu yöntem `return View();`tarayıcıya bir yanıt işlemek için bir görünüm şablondosyası kullanması gerektiğini belirten deyimi koştu. Görünüm şablonu dosya adı belirtilmedi, MVC varsayılan görünüm dosyasını kullanmaya varsayılan olarak. Varsayılan görünüm dosyası yöntemle aynı ada sahiptir (`Index`), bu nedenle */Views/HelloWorld/Index.cshtml* kullanılır. Aşağıdaki resimde "Görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir. görünümünde sabit kodlanmış.

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Görünümleri ve düzen sayfalarını değiştirme

Menü bağlantılarını seçin **(MvcMovie**, **Home**, ve **Privacy**). Her sayfa aynı menü düzenini gösterir. Menü düzeni *Görünümler/Paylaşılan/_Layout.cshtml* dosyasında uygulanır. *Görünümler/Paylaşılan/_Layout.cshtml* dosyasını açın.

[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı düzenini tek bir yerde belirtmenize ve ardından sitenizdeki birden çok sayfaya uygulamanıza olanak tanır. `@RenderBody()` Hattı bul. `RenderBody`oluşturduğunuz tüm görünüme özel sayfaların düzen sayfasına sarılmış olarak *görüntülendiği* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Ev/Gizlilik.cshtml** görünümü `RenderBody` yöntemin içinde işlenir.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme

* Başlık ve altbilgi öğelerinde, `Movie App`'' olarak değiştirin `MvcMovie`
* Bağlantı elemanını `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`' ile değiştirme

Aşağıdaki biçimlendirme vurgulanan değişiklikleri gösterir:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

Önceki biçimlendirmede, `asp-area` bu uygulama [Alanlar'ı](xref:mvc/controllers/areas)kullanmadığından, bağlantı etiketi [Yardımcı özelliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) atlandı.

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Not**: `Movies` Denetleyici uygulanmadı. Bu noktada, `Movie App` bağlantı işlevsel değildir.

Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin. Tarayıcı sekmesindeki başlığın **Gizlilik Politikası 'nı** nasıl görüntülenebilene dikkat edin - Gizlilik Politikası yerine Film Uygulaması **- Mvc Movie**:

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Ana **Sayfa** bağlantısını seçin ve başlık ve bağlantı metninin de **Film Uygulamasını**görüntülediğini fark edin. Biz düzen şablonu bir kez değişiklik yapmak ve sitedeki tüm sayfaları yeni bağlantı metni ve yeni başlık yansıtacak başardık.

*Görünümler/_ViewStart.cshtml* dosyasını inceleyin:

```cshtml
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* dosyası, her görünüme *Görünümler/Paylaşılan/_Layout.cshtml* dosyasını getirir. Özellik, `Layout` farklı bir düzen görünümü ayarlamak veya hiçbir `null` düzen dosyasının kullanılmaması için ayarlamak için kullanılabilir.

`<h2>` *Görünümler/HelloWorld/Index.cshtml* görünüm dosyasının başlığını ve öğesini değiştirin:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Başlık ve `<h2>` öğe biraz farklı olduğundan, hangi kod parçasının ekranı değiştirdiğini görebilirsiniz.

`ViewData["Title"] = "Movie List";`yukarıdaki kodda sözlüğün `Title` `ViewData` özelliğini "Film Listesi" olarak ayarlar. Özellik, `Title` düzen sayfasındaki `<title>` HTML öğesinde kullanılır:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Değişikliği kaydedin `https://localhost:{PORT}/HelloWorld`ve ''ye gidin. Tarayıcı başlığının, birincil başlığın ve ikincil başlıkların değiştiğine dikkat edin. (Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülenmiş olabilirsiniz. Sunucudan gelen yanıtı yüklemeye zorlamak için tarayıcınızda Ctrl+F5 tuşuna basın.) Tarayıcı `ViewData["Title"]` *başlığı, Index.cshtml* görünüm şablonuna ve düzen dosyasına eklenen ek "- Film Uygulaması" ile oluşturulur.

*Ayrıca Index.cshtml* görünüm şablonundaki içeriğin *Görünümler/Paylaşılan/_Layout.cshtml* görünüm şablonuyla nasıl birleştirilmeye ve tarayıcıya tek bir HTML yanıtı gönderildiğine de dikkat edin. Düzen şablonları, uygulamanızdaki tüm sayfalarda geçerli olan değişiklikleri yapmayı gerçekten kolaylaştırır. Daha fazla bilgi için [Bkz. Düzen](xref:mvc/views/layout).

![Film Listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Bizim küçük "veri" (bu durumda "Merhaba Bizim Görünüm Şablonu!" mesaj) olsa da, sabit kodlanmış. MVC uygulaması "V" (görünüm) ve bir "C" (denetleyici) var, ama hiçbir "M" (model) henüz.

## <a name="passing-data-from-the-controller-to-the-view"></a>Denetleyiciden Görünüme Veri Aktarma

Denetleyici eylemleri, gelen bir URL isteğine yanıt olarak çağrılır. Denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir. Denetleyici bir veri kaynağından veri alır ve tarayıcıya geri göndermek için ne tür bir yanıt karar verir. Görünüm şablonları tarayıcıya bir HTML yanıtı oluşturmak ve biçimlendirmek için bir denetleyiciden kullanılabilir.

Denetleyiciler, bir görünüm şablonuna yanıt verebilmesi için gereken verileri sağlamakla yükümlüdür. En iyi yöntem: Görünüm şablonları iş mantığı **gerçekleştirmemeli** veya doğrudan bir veritabanıyla etkileşime girmemelidir. Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sağlanan verilerle çalışmalıdır. Bu "endişeleri ayırma" bakımı, kodun temiz, sınanabilir ve korunabilir tutulmasına yardımcı olur.

Şu `Welcome` anda, `HelloWorldController` sınıftaki `name` yöntem `ID` bir ve bir parametre alır ve sonra değerleri doğrudan tarayıcıya çıkarır. Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, bunun yerine görünüm şablonu kullanmak için denetleyiciyi değiştirin. Görünüm şablonu dinamik bir yanıt oluşturur, bu da yanıtı oluşturmak için uygun veri bitlerinin denetleyiciden görünüme geçirilmesi gerektiği anlamına gelir. Denetleyicinin görünüm şablonuna ihtiyaç duyduğu dinamik verileri (parametreleri) görünüm şablonuna erişebileceği bir `ViewData` sözlükte koyarak bunu yapın.

*HelloWorldController.cs,* sözlük `Welcome` için bir `Message` ve `NumTimes` değer `ViewData` eklemek için yöntemi değiştirin. Sözlük `ViewData` dinamik bir nesnedir, bu da herhangi bir türün kullanılabildiği anlamına gelir; nesnenin içine bir şey koyana `ViewData` kadar tanımlı özellikleri yoktur. [MVC model bağlama sistemi,](xref:mvc/models/model-binding) adres çubuğundaki `numTimes`sorgu dizesinden adı geçen parametreleri (ve)`name` yönteminizdeki parametrelere otomatik olarak eşler. Tam *HelloWorldController.cs* dosyası aşağıdaki gibi görünür:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Sözlük `ViewData` nesnesi görünüme geçirilecek verileri içerir.

*Görünümler/HelloWorld/Welcome.cshtml*adlı hoş geldiniz görünümü şablonu oluşturun.

*Welcome.cshtml* görünüm şablonunda "Merhaba" `NumTimes`görüntülenen bir döngü oluşturursunuz. *Views/HelloWorld/Welcome.cshtml* içeriğini aşağıdakilerle değiştirin:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Değişikliklerinizi kaydedin ve aşağıdaki URL'ye göz atın:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Veriler URL'den alınır ve [MVC model bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye aktarılır. Denetleyici verileri bir `ViewData` sözlük tesidikkate alır ve bu nesneyi görünüme geçirir. Görünüm daha sonra verileri tarayıcıya HTML olarak işler.

![Hoş geldiniz etiketini ve Hello Rick ifadesini gösteren gizlilik görünümü dört kez gösterilir](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Yukarıdaki örnekte, `ViewData` sözlük denetleyiciden bir görünüme veri aktarmak için kullanılmıştır. Daha sonra öğreticide, bir görünüm modeli denetleyiciden görünüme veri aktarmak için kullanılır. Veri aktarılabilmek için görünüm modeli yaklaşımı `ViewData` genellikle sözlük yaklaşımına göre daha çok tercih edilir. Daha fazla bilgi [için ViewBag, ViewData veya TempData'nın ne zaman kullanılacağına](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) bakın.

Bir sonraki öğreticide, bir film veritabanı oluşturulur.

> [!div class="step-by-step"]
> [Önceki](adding-controller.md)
> [Sonraki](adding-model.md)

::: moniker-end
