---
title: "Öğretici: JavaScript ile ASP.NET Core Web API 'SI çağırma"
author: rick-anderson
description: JavaScript ile ASP.NET Core Web API 'sini çağırmayı öğrenin.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94703715"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Öğretici: JavaScript ile ASP.NET Core Web API 'SI çağırma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğreticide, [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanılarak JavaScript ile ASP.NET Core Web API 'sinin nasıl çağrılacağını gösterilmektedir.

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,2 için, [JavaScript ile Web API 'Sini çağırma](xref:tutorials/first-web-api#call-the-web-api-with-javascript)2,2 sürümüne bakın.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Ön koşullar

* Tüm [öğreticiyi: Web API 'Si oluşturma](xref:tutorials/first-web-api)
* CSS, HTML ve JavaScript ile benzerlik

## <a name="call-the-web-api-with-javascript"></a>JavaScript ile Web API 'sini çağırma

Bu bölümde, Yapılacaklar öğeleri oluşturmak ve yönetmek için form içeren bir HTML sayfası ekleyeceksiniz. Olay işleyicileri sayfadaki öğelere iliştirilir. Olay işleyicileri, Web API 'sinin eylem yöntemlerine HTTP istekleri sonucu vermez. Fetch API 'nin `fetch` işlevi her http isteğini başlatır.

`fetch`İşlevi, nesne olarak temsil edilen BIR http yanıtı içeren bir [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) nesnesi döndürür `Response` . Ortak bir model, nesnesinde işlevini çağırarak JSON yanıt gövdesini ayıklamaya yönelik olur `json` `Response` . JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.

En basit `fetch` çağrı, yolu temsil eden tek bir parametre kabul eder. Nesne olarak bilinen ikinci bir parametre `init` isteğe bağlıdır. `init` HTTP isteğini yapılandırmak için kullanılır.

1. Uygulamayı [statik dosyaları sunacak](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [varsayılan dosya eşlemesini etkinleştirecek](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)şekilde yapılandırın. Aşağıdaki vurgulanan kod, `Configure` *Startup.cs* yönteminde gereklidir:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Proje kökünde bir *Wwwroot* klasörü oluşturun.

1. *Wwwroot* klasörü içinde bir *js* klasörü oluşturun.

1. *Wwwroot* klasörüne *index.html* adlı bir HTML dosyası ekleyin. *index.html* öğesinin içeriğini aşağıdaki biçimlendirme ile değiştirin:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. *Wwwroot/CSS* klasörüne *site. CSS* adlı bir CSS dosyası ekleyin. *Site. css* içeriğini aşağıdaki stillerle değiştirin:

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. *Wwwroot/js* klasörüne *site.js* adlı bir JavaScript dosyası ekleyin. *site.js* içeriğini aşağıdaki kodla değiştirin:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:

1. *ÜzerindeProperties\launchSettings.js* açın.
1. `launchUrl`Uygulamayı projenin varsayılan dosyasında *index.html*'de açmaya zorlamak için özelliği kaldırın &mdash; .

Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır. Web API isteklerinin açıklamaları aşağıda verilmiştir.

### <a name="get-a-list-of-to-do-items"></a>Yapılacaklar öğelerinin bir listesini alın

Aşağıdaki kodda, *API/todoıtems* yoluna BIR http get isteği gönderilir:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Web API 'SI başarılı bir durum kodu döndürdüğünde, `_displayItems` işlev çağrılır. Tarafından kabul edilen dizi parametresindeki her Yapılacaklar öğesi `_displayItems` , **Düzenle** ve **Sil** düğmeleri içeren bir tabloya eklenir. Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.

### <a name="add-a-to-do-item"></a>Yapılacaklar öğesi ekleme

Aşağıdaki kodda:

* Bir `item` değişken, yapılacaklar öğesinin nesne sabit gösterimini oluşturmak için bildirilmiştir.
* Aşağıdaki seçeneklerle bir getirme isteği yapılandırılır:
  * `method`&mdash;HTTP SONRASı eylem fiilini belirtir.
  * `body`&mdash;istek gövdesinin JSON temsilini belirtir. JSON, içinde depolanan nesne sabit değeri `item` [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) işlevine geçirilerek oluşturulur.
  * `headers`&mdash;`Accept`ve `Content-Type` http istek üst bilgilerini belirtir. Her iki üst bilgi `application/json` , sırasıyla alınan ve gönderilen medya türünü belirtmek için olarak ayarlanır.
* *API/todoıtems* yoluna BIR http post isteği gönderilir.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Web API 'SI başarılı bir durum kodu döndürdüğünde, `getItems` Işlev HTML tablosunu güncelleştirmek için çağrılır. Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.

### <a name="update-a-to-do-item"></a>Yapılacaklar öğesini güncelleştirme

Bir yapılacaklar öğesinin güncelleştirilmesi bir tane eklemeye benzer; Ancak, iki önemli fark vardır:

* Yol, güncelleştirilecek öğenin benzersiz tanımlayıcısı ile sone düzeltildi. Örneğin, *api/todoıtems/1*.
* HTTP eylemi fiili, seçeneğinde gösterildiği gibi konur `method` .

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Bir yapılacaklar öğesini silme

Bir yapılacaklar öğesini silmek için isteğin `method` seçeneğini olarak ayarlayın `DELETE` ve URL 'de öğenin benzersiz tanımlayıcısını belirtin.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Web API 'SI yardım sayfaları oluşturmayı öğrenmek için bir sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
