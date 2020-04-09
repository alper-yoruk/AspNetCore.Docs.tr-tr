---
title: "Öğretici: JavaScript ile ASP.NET Core web API'yi arayın"
author: rick-anderson
description: JavaScript ile ASP.NET Core web API'yi nasıl arayacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655255"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Öğretici: JavaScript ile ASP.NET Core web API'yi arayın

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğretici, [ApI Getir'i](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanarak JavaScript ile ASP.NET Core web API'yi nasıl çağıracağımı gösterir.

::: moniker range="< aspnetcore-3.0"

Core 2.2ASP.NET [için, JavaScript ile web API'yi Arayın'ın](xref:tutorials/first-web-api#call-the-web-api-with-javascript)2.2 sürümüne bakın.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Ön koşullar

* Komple [Öğretici: Bir web API oluşturma](xref:tutorials/first-web-api)
* CSS, HTML ve JavaScript ile aşinalık

## <a name="call-the-web-api-with-javascript"></a>JavaScript ile web API'sini arayın

Bu bölümde, yapılacak lar öğeleri oluşturmak ve yönetmek için formlar içeren bir HTML sayfası eklersiniz. Olay işleyicileri sayfadaki öğelere eklenir. Olay işleyicileri, web API'nin eylem yöntemlerine http istekleriyle sonuçlanır. Api Getir `fetch` işlevi her BIR HTTP isteğini başlatır.

İşlev, `fetch` [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) `Response` nesne olarak temsil edilen bir HTTP yanıtı içeren bir Söz nesnesini döndürür. Yaygın bir desen `json` `Response` nesne üzerinde işlevi çağırarak JSON yanıt gövdesi ayıklamaktır. JavaScript, sayfayı web API'sının yanıtındaki ayrıntılarla güncelleştirir.

En basit `fetch` arama, rotayı temsil eden tek bir parametreyi kabul eder. `init` Nesne olarak bilinen ikinci bir parametre isteğe bağlıdır. `init`HTTP isteğini yapılandırmak için kullanılır.

1. Uygulamayı [statik dosyalara hizmet etmek](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve varsayılan dosya [eşlemesini etkinleştirecek](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)şekilde yapılandırın. Aşağıdaki vurgulanan kod *Startup.cs* `Configure` yöntemi gereklidir:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Proje kökünde bir *wwwroot* klasörü oluşturun.

1. *wwwroot* klasörünün içinde bir *js* klasörü oluşturun.

1. *wwwroot* klasörüne *index.html* adlı bir HTML dosyası ekleyin. *index.html* içeriğini aşağıdaki biçimlendirmeyle değiştirin:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. *wwwroot/js* klasörüne *site.js* adında bir JavaScript dosyası ekleyin. *site.js* içeriğini aşağıdaki kodla değiştirin:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarında bir değişiklik gerekebilir:

1. *Özellikleri Aç\launchSettings.json*.
1. Uygulamayı `launchUrl` projenin varsayılan *dosyasıindex.html'de*&mdash;açmaya zorlamak için özelliği kaldırın.

Bu örnek, web API'sının tüm CRUD yöntemlerini çağırır. Aşağıda web API isteklerinin açıklamaları veremeleri yer alıyor.

### <a name="get-a-list-of-to-do-items"></a>Yapılacak lar listesi alın

Aşağıdaki kodda, *api/TodoItems* rotasına bir HTTP GET isteği gönderilir:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Web API başarılı bir durum kodu `_displayItems` döndürdüğünde, işlev çağrılır. Kabul `_displayItems` edilen dizi parametresindeki yapılacaklar öğesi **Düzenle** ve **Sil** düğmelerinin yer aldığı bir tabloya eklenir. Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.

### <a name="add-a-to-do-item"></a>Yapılacaklar öğesi ekleme

Aşağıdaki kodda:

* Bir `item` değişken, yapılacaklar öğesinin nesne gerçek gösterimini oluşturmak için bildirilir.
* Bir Getir isteği aşağıdaki seçeneklerle yapılandırılır:
  * `method`&mdash;POST HTTP eylem fiilini belirtir.
  * `body`&mdash;talep organının JSON temsilini belirtir. JSON, depolanan nesnenin `item` [json.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) işlevine aktarılmasıyla üretilir.
  * `headers`&mdash;ve `Content-Type` HTTP `Accept` istek üstbilgilerini belirtir. Her iki üstbilgi `application/json` de sırasıyla alınan ve gönderilen ortam türünü belirtmek için ayarlanır.
* *API/TodoItems* rotasına bir HTTP POST isteği gönderilir.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Web API başarılı bir durum kodu `getItems` döndürdüğünde, işlev HTML tablosunu güncelleştirmek için çağrılır. Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.

### <a name="update-a-to-do-item"></a>Yapılacaklar öğesini güncelleştirme

Yapılacaklar öğesini güncelleştirmek, bir öğe eklemeye benzer; ancak, iki önemli fark vardır:

* Rota, güncelleştirilen öğenin benzersiz tanımlayıcısıyla sabitlenir. Örneğin, *api/TodoItems/1*.
* HTTP eylem fiili, `method` seçenekte belirtildiği gibi PUT'dur.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Yapılacaklar öğesini silme

Yapılacaklar öğesini silmek için, isteğin `method` `DELETE` seçeneğini belirleyin ve öğenin URL'deki benzersiz tanımlayıcısını belirtin.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Web API yardım sayfalarını nasıl oluşturacağınızı öğrenmek için bir sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
