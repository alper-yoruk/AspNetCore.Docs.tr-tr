---
title: ASP.NET Core ile yerel mobil uygulamalar için arka uç hizmetleri oluşturma
author: ardalis
description: Yerel mobil uygulamaları desteklemek için ASP.NET Core MVC kullanarak arka uç hizmetleri oluşturmayı öğrenin.
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 0bbf740cb49b77b476e7e015afee311110bbe5ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060995"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>ASP.NET Core ile yerel mobil uygulamalar için arka uç hizmetleri oluşturma

[Steve Smith](https://ardalis.com/) tarafından

Mobil uygulamalar ASP.NET Core arka uç hizmetleriyle iletişim kurabilir. İOS simülatörleri ve Android öykünücülerinden yerel Web hizmetlerini bağlama yönergeleri için bkz. [IOS simülatörleri ve Android Öykünücülerinden yerel Web hizmetlerine bağlanma](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Örnek arka uç hizmetleri kodunu görüntüle veya indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>Örnek yerel mobil uygulama

Bu öğreticide, yerel mobil uygulamaları desteklemek üzere ASP.NET Core MVC kullanarak arka uç hizmetleri oluşturma gösterilmektedir. Android, iOS, Windows Evrensel ve Windows Phone cihazları için ayrı yerel istemciler içeren, [Xamarin Forms ToDoRest uygulamasını](/xamarin/xamarin-forms/data-cloud/consuming/rest) yerel istemcisi olarak kullanır. Yerel uygulamayı oluşturmak (ve gerekli ücretsiz Xamarin araçlarını yüklemek) ve Xamarin örnek çözümünü indirmek için bağlantılı öğreticiyi izleyebilirsiniz. Xamarin örneği, bu makalenin ASP.NET Core uygulamasının yerini aldığı bir ASP.NET Web API 2 Services projesi içerir (istemci tarafından gerekli olmayan değişiklikler olmadan).

![Android akıllı telefonundaki Rest uygulamasını çalıştırmak için](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Özellikler

ToDoRest uygulaması, To-Do öğelerinin listesini, eklenmesini, silinmesini ve güncelleştirilmesini destekler. Her öğe bir KIMLIĞE, bir ada, notlara ve henüz gerçekleştirilip yapılmadığını gösteren bir özelliğe sahiptir.

Öğelerin ana görünümü yukarıda gösterildiği gibi, her öğenin adını listeler ve bir onay işaretiyle gerçekleştirilip yapılmadığını gösterir.

Simgeye dokunarak `+` öğe Ekle iletişim kutusu açılır:

![Öğe Ekle iletişim kutusu](native-mobile-backend/_static/todo-android-new-item.png)

Ana liste ekranındaki bir öğeye dokunduğunuzda, öğenin adı, notları ve bitti ayarlarının değiştirilebilmesi veya öğenin silinebileceği bir düzenleme iletişim kutusu açılır.

![Öğeyi Düzenle iletişim kutusu](native-mobile-backend/_static/todo-android-edit-item.png)

Bu örnek varsayılan olarak, salt okuma işlemlerine izin veren developer.xamarin.com adresinde barındırılan arka uç hizmetlerini kullanacak şekilde yapılandırılmıştır. Bilgisayarınızı bilgisayarınızda çalışan bir sonraki bölümde oluşturulan ASP.NET Core uygulamasına karşı test etmek için uygulamanın sabitini güncelleştirmeniz gerekir `RestUrl` . `ToDoREST`Projeye gidin ve *Constants.cs* dosyasını açın. Değerini `RestUrl` MAKINENIZIN IP adresini (localhost veya 127.0.0.1 değil) içeren BIR URL ile değiştirin. bu adres, makineden değil cihaz öykünücüsünde kullanılıyor. Bağlantı noktası numarasını da (5000) dahil edin. Hizmetlerinizin bir cihazla birlikte çalıştığını test etmek için, bu bağlantı noktasına erişimi engelleyen etkin bir güvenlik duvarı olmadığından emin olun.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>ASP.NET Core projesi oluşturma

Visual Studio 'da yeni bir ASP.NET Core Web uygulaması oluşturun. Web API şablonunu ve kimlik doğrulaması yok ' ı seçin. Projeyi *ToDoApi* olarak adlandırın.

![Web API 'SI proje şablonu seçiliyken yeni ASP.NET Web uygulaması iletişim kutusu](native-mobile-backend/_static/web-api-template.png)

Uygulama, 5000 numaralı bağlantı noktasına yapılan tüm isteklere yanıt vermelidir. Bunu sağlamak için *program.cs* güncelleştirmesini ekleyin `.UseUrls("http://*:5000")` :

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Varsayılan olarak yerel olmayan istekleri yok sayan IIS Express arkasında değil, uygulamayı doğrudan çalıştırdığınızdan emin olun. Bir komut isteminden [DotNet Run](/dotnet/core/tools/dotnet-run) komutunu çalıştırın veya Visual Studio araç çubuğunda hata ayıklama hedefi açılır listesinden uygulama adı profilini seçin.

To-Do öğelerini temsil etmek için bir model sınıfı ekleyin. Gerekli alanları `[Required]` özniteliğiyle işaretle:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

API yöntemleri, verilerle çalışmak için bir yol gerektirir. `IToDoRepository`Özgün Xamarin örneğinin kullandığı arabirimi kullanın:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

Bu örnek için, uygulama yalnızca özel bir öğe koleksiyonu kullanır:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

*Startup.cs* 'de uygulamayı yapılandırın:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

Bu noktada, *ToDoItemsController* oluşturmak için hazırsınız demektir.

> [!TIP]
> [ASP.NET Core MVC ve Visual Studio ile Ilk Web API 'Nizi derleme](../tutorials/first-web-api.md)bölümünde Web API 'leri oluşturma hakkında daha fazla bilgi edinin.

## <a name="creating-the-controller"></a>Denetleyici oluşturma

Projeye yeni bir denetleyici ekleyin, *ToDoItemsController* . Microsoft. AspNetCore. Mvc. Controller öğesinden devralması gerekir. `Route`Denetleyicinin ile başlayan yollara yapılan istekleri işleyeceğini göstermek için bir öznitelik ekleyin `api/todoitems` . `[controller]`Yoldaki belirteç, denetleyicinin adı ile değiştirilmiştir ( `Controller` son eki atlayarak) ve özellikle genel yollar için yararlıdır. [Yönlendirme](../fundamentals/routing.md)hakkında daha fazla bilgi edinin.

Denetleyici bir `IToDoRepository` to işlevi gerektirir; denetleyicinin Oluşturucusu aracılığıyla bu türde bir örnek isteyin. Çalışma zamanında bu örnek, çerçevenin [bağımlılık ekleme](../fundamentals/dependency-injection.md)desteği kullanılarak sağlanacaktır.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Bu API, veri kaynağında CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri gerçekleştirmek için dört farklı HTTP fiillerini destekler. Bunların en basit yolu, bir HTTP GET isteğine karşılık gelen okuma işlemidir.

### <a name="reading-items"></a>Öğeleri okuma

Bir öğe listesi istemek yönteme bir GET isteğiyle yapılır `List` . `[HttpGet]`Yöntemi üzerindeki özniteliği, `List` Bu EYLEMIN yalnızca Get isteklerini işleyeceği anlamına gelir. Bu eylemin yolu, denetleyicide belirtilen yoldur. Yolun bir parçası olarak eylem adını kullanmanız gerekmez. Her bir eylemin benzersiz ve belirsiz bir yol içerdiğinden emin olmanız yeterlidir. Yönlendirme öznitelikleri, belirli yolları oluşturmak için hem denetleyiciye hem de Yöntem düzeylerine uygulanabilir.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

`List`Yöntemi, JSON olarak serileştirilmiş bir 200 Tamam yanıt kodu ve tüm Todo öğelerini döndürür.

Yeni API yönteminizi, [Postman](https://www.getpostman.com/docs/)gibi çeşitli araçlar kullanarak test edebilirsiniz:

![Postman konsolu, todoıtems için bir GET isteği ve döndürülen üç öğe için JSON 'ı gösteren yanıtın gövdesini gösterir](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Öğe oluşturma

Kurala göre, yeni veri öğeleri oluşturmak HTTP POST fiili ile eşleştirilir. `Create`Yöntemine `[HttpPost]` uygulanan bir özniteliği vardır ve bir örneği kabul eder `ToDoItem` . `item`Bağımsız DEĞIŞKEN gönderi gövdesinde geçirildiğinden, bu parametre `[FromBody]` özniteliğini belirtir.

Yöntemi içinde, öğe geçerliliği için denetlenir ve veri deposunda daha önce bulunur ve herhangi bir sorun oluşursa, depo kullanılarak eklenir. `ModelState.IsValid` [Model doğrulaması](../mvc/models/validation.md)gerçekleştirir ve Kullanıcı girişini kabul eden her API yönteminde yapılmalıdır.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

Örnek, mobil istemciye geçirilen hata kodlarını içeren bir Enum kullanır:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

İsteğin gövdesinde JSON biçiminde yeni nesne sağlayan POST fiilini kullanarak yeni öğe ekleme işlemini test edin. Ayrıca, öğesini belirten bir istek üst bilgisi eklemeniz `Content-Type` gerekir `application/json` .

![GÖNDERI ve yanıt gösteren Postman konsolu](native-mobile-backend/_static/postman-post.png)

Yöntemi, yanıtta yeni oluşturulan öğeyi döndürür.

### <a name="updating-items"></a>Öğeler güncelleştiriliyor

Kayıtları değiştirme HTTP PUT istekleri kullanılarak yapılır. Bu değişiklik dışında, `Edit` yöntemi neredeyse aynıdır `Create` . Kayıt bulunamazsa, `Edit` eylem bir `NotFound` (404) yanıtı döndürür.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Postman ile test etmek için fiili ' i koymak için değiştirin. İsteğin gövdesinde güncelleştirilmiş nesne verilerini belirtin.

![Bir PUT ve Response gösteren Postman konsolu](native-mobile-backend/_static/postman-put.png)

Bu yöntem `NoContent` , başarılı olduğunda, önceden var olan API ile tutarlılık için (204) yanıtını döndürür.

### <a name="deleting-items"></a>Öğeleri silme

Kayıtları silme işlemi, hizmete SILME istekleri yapılarak ve silinecek öğenin KIMLIĞI geçirilerek gerçekleştirilir. Güncelleştirmelerde olduğu gibi, mevcut olmayan öğeler için istekler `NotFound` yanıt alır. Aksi takdirde, başarılı bir istek `NoContent` (204) yanıtını alır.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Silme işlevinin test edilirken, isteğin gövdesinde hiçbir şey gerekmediği unutulmamalıdır.

![Bir DELETE ve Response gösteren Postman konsolu](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Ortak Web API kuralları

Uygulamanız için arka uç hizmetlerini geliştirirken, çapraz kesme sorunlarını işlemeye yönelik tutarlı bir kural veya ilke kümesiyle birlikte çalışmak isteyeceksiniz. Örneğin, yukarıda gösterilen hizmette, bulunamayan belirli kayıtlara yönelik istekler `NotFound` Yanıt yerine bir yanıt aldı `BadRequest` . Benzer şekilde, bu hizmete, model bağlantılı türler 'e geçirilen komutlar her zaman denetlenir `ModelState.IsValid` ve `BadRequest` geçersiz model türleri için döndürülür.

API 'leriniz için ortak bir ilke tanımladıktan sonra, genellikle bunu bir [filtrede](../mvc/controllers/filters.md)kapsülleyebilirsiniz. [ASP.NET Core MVC uygulamalarında ortak API ilkelerini kapsüllemek](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)hakkında daha fazla bilgi edinin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kimlik doğrulama ve yetkilendirme](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
