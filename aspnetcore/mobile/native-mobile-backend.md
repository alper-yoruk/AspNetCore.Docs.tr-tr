---
title: ASP.NET Core ile yerel mobil uygulamalar için arka uç hizmetleri oluşturma
author: ardalis
description: Yerel mobil uygulamaları desteklemek için ASP.NET Core MVC kullanarak arka uç hizmetleri oluşturmayı öğrenin.
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 00e0c93d200e7eec6fd6bdac1f1246fe0909fc54
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630542"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="73660-103">ASP.NET Core ile yerel mobil uygulamalar için arka uç hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="73660-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="73660-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="73660-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="73660-105">Mobil uygulamalar ASP.NET Core arka uç hizmetleriyle iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="73660-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="73660-106">İOS simülatörleri ve Android öykünücülerinden yerel Web hizmetlerini bağlama yönergeleri için bkz. [IOS simülatörleri ve Android Öykünücülerinden yerel Web hizmetlerine bağlanma](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="73660-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="73660-107">Örnek arka uç hizmetleri kodunu görüntüle veya indir</span><span class="sxs-lookup"><span data-stu-id="73660-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="73660-108">Örnek yerel mobil uygulama</span><span class="sxs-lookup"><span data-stu-id="73660-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="73660-109">Bu öğreticide, yerel mobil uygulamaları desteklemek üzere ASP.NET Core MVC kullanarak arka uç hizmetleri oluşturma gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="73660-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="73660-110">Android, iOS, Windows Evrensel ve Windows Phone cihazları için ayrı yerel istemciler içeren, [Xamarin Forms ToDoRest uygulamasını](/xamarin/xamarin-forms/data-cloud/consuming/rest) yerel istemcisi olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="73660-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="73660-111">Yerel uygulamayı oluşturmak (ve gerekli ücretsiz Xamarin araçlarını yüklemek) ve Xamarin örnek çözümünü indirmek için bağlantılı öğreticiyi izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="73660-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="73660-112">Xamarin örneği, bu makalenin ASP.NET Core uygulamasının yerini aldığı bir ASP.NET Web API 2 Services projesi içerir (istemci tarafından gerekli olmayan değişiklikler olmadan).</span><span class="sxs-lookup"><span data-stu-id="73660-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Android akıllı telefonundaki Rest uygulamasını çalıştırmak için](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="73660-114">Özellikler</span><span class="sxs-lookup"><span data-stu-id="73660-114">Features</span></span>

<span data-ttu-id="73660-115">ToDoRest uygulaması, yapılacaklar öğelerini listelemeyi, eklemeyi, silmeyi ve güncellemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="73660-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="73660-116">Her öğe bir KIMLIĞE, bir ada, notlara ve henüz gerçekleştirilip yapılmadığını gösteren bir özelliğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="73660-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="73660-117">Öğelerin ana görünümü yukarıda gösterildiği gibi, her öğenin adını listeler ve bir onay işaretiyle gerçekleştirilip yapılmadığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="73660-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="73660-118">Simgeye dokunarak `+` öğe Ekle iletişim kutusu açılır:</span><span class="sxs-lookup"><span data-stu-id="73660-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Öğe Ekle iletişim kutusu](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="73660-120">Ana liste ekranındaki bir öğeye dokunduğunuzda, öğenin adı, notları ve bitti ayarlarının değiştirilebilmesi veya öğenin silinebileceği bir düzenleme iletişim kutusu açılır.</span><span class="sxs-lookup"><span data-stu-id="73660-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Öğeyi Düzenle iletişim kutusu](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="73660-122">Bu örnek varsayılan olarak, salt okuma işlemlerine izin veren developer.xamarin.com adresinde barındırılan arka uç hizmetlerini kullanacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="73660-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="73660-123">Bilgisayarınızı bilgisayarınızda çalışan bir sonraki bölümde oluşturulan ASP.NET Core uygulamasına karşı test etmek için uygulamanın sabitini güncelleştirmeniz gerekir `RestUrl` .</span><span class="sxs-lookup"><span data-stu-id="73660-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="73660-124">`ToDoREST`Projeye gidin ve *Constants.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="73660-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="73660-125">Değerini `RestUrl` MAKINENIZIN IP adresini (localhost veya 127.0.0.1 değil) içeren BIR URL ile değiştirin. bu adres, makineden değil cihaz öykünücüsünde kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="73660-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="73660-126">Bağlantı noktası numarasını da (5000) dahil edin.</span><span class="sxs-lookup"><span data-stu-id="73660-126">Include the port number as well (5000).</span></span> <span data-ttu-id="73660-127">Hizmetlerinizin bir cihazla birlikte çalıştığını test etmek için, bu bağlantı noktasına erişimi engelleyen etkin bir güvenlik duvarı olmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="73660-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="73660-128">ASP.NET Core projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="73660-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="73660-129">Visual Studio 'da yeni bir ASP.NET Core Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="73660-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="73660-130">Web API şablonunu ve kimlik doğrulaması yok ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="73660-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="73660-131">Projeyi *ToDoApi*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="73660-131">Name the project *ToDoApi*.</span></span>

![Web API 'SI proje şablonu seçiliyken yeni ASP.NET Web uygulaması iletişim kutusu](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="73660-133">Uygulama, 5000 numaralı bağlantı noktasına yapılan tüm isteklere yanıt vermelidir.</span><span class="sxs-lookup"><span data-stu-id="73660-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="73660-134">Bunu sağlamak için *program.cs* güncelleştirmesini ekleyin `.UseUrls("http://*:5000")` :</span><span class="sxs-lookup"><span data-stu-id="73660-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="73660-135">Varsayılan olarak yerel olmayan istekleri yok sayan IIS Express arkasında değil, uygulamayı doğrudan çalıştırdığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="73660-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="73660-136">Bir komut isteminden [DotNet Run](/dotnet/core/tools/dotnet-run) komutunu çalıştırın veya Visual Studio araç çubuğunda hata ayıklama hedefi açılır listesinden uygulama adı profilini seçin.</span><span class="sxs-lookup"><span data-stu-id="73660-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="73660-137">Yapılacaklar öğelerini temsil etmek için bir model sınıfı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="73660-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="73660-138">Gerekli alanları `[Required]` özniteliğiyle işaretle:</span><span class="sxs-lookup"><span data-stu-id="73660-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="73660-139">API yöntemleri, verilerle çalışmak için bir yol gerektirir.</span><span class="sxs-lookup"><span data-stu-id="73660-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="73660-140">`IToDoRepository`Özgün Xamarin örneğinin kullandığı arabirimi kullanın:</span><span class="sxs-lookup"><span data-stu-id="73660-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="73660-141">Bu örnek için, uygulama yalnızca özel bir öğe koleksiyonu kullanır:</span><span class="sxs-lookup"><span data-stu-id="73660-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="73660-142">*Startup.cs*'de uygulamayı yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="73660-142">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="73660-143">Bu noktada, *ToDoItemsController*oluşturmak için hazırsınız demektir.</span><span class="sxs-lookup"><span data-stu-id="73660-143">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="73660-144">[ASP.NET Core MVC ve Visual Studio ile Ilk Web API 'Nizi derleme](../tutorials/first-web-api.md)bölümünde Web API 'leri oluşturma hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="73660-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="73660-145">Denetleyici oluşturma</span><span class="sxs-lookup"><span data-stu-id="73660-145">Creating the Controller</span></span>

<span data-ttu-id="73660-146">Projeye yeni bir denetleyici ekleyin, *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="73660-146">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="73660-147">Microsoft. AspNetCore. Mvc. Controller öğesinden devralması gerekir.</span><span class="sxs-lookup"><span data-stu-id="73660-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="73660-148">`Route`Denetleyicinin ile başlayan yollara yapılan istekleri işleyeceğini göstermek için bir öznitelik ekleyin `api/todoitems` .</span><span class="sxs-lookup"><span data-stu-id="73660-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="73660-149">`[controller]`Yoldaki belirteç, denetleyicinin adı ile değiştirilmiştir ( `Controller` son eki atlayarak) ve özellikle genel yollar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="73660-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="73660-150">[Yönlendirme](../fundamentals/routing.md)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="73660-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="73660-151">Denetleyici bir `IToDoRepository` to işlevi gerektirir; denetleyicinin Oluşturucusu aracılığıyla bu türde bir örnek isteyin.</span><span class="sxs-lookup"><span data-stu-id="73660-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="73660-152">Çalışma zamanında bu örnek, çerçevenin [bağımlılık ekleme](../fundamentals/dependency-injection.md)desteği kullanılarak sağlanacaktır.</span><span class="sxs-lookup"><span data-stu-id="73660-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="73660-153">Bu API, veri kaynağında CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri gerçekleştirmek için dört farklı HTTP fiillerini destekler.</span><span class="sxs-lookup"><span data-stu-id="73660-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="73660-154">Bunların en basit yolu, bir HTTP GET isteğine karşılık gelen okuma işlemidir.</span><span class="sxs-lookup"><span data-stu-id="73660-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="73660-155">Öğeleri okuma</span><span class="sxs-lookup"><span data-stu-id="73660-155">Reading Items</span></span>

<span data-ttu-id="73660-156">Bir öğe listesi istemek yönteme bir GET isteğiyle yapılır `List` .</span><span class="sxs-lookup"><span data-stu-id="73660-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="73660-157">`[HttpGet]`Yöntemi üzerindeki özniteliği, `List` Bu EYLEMIN yalnızca Get isteklerini işleyeceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="73660-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="73660-158">Bu eylemin yolu, denetleyicide belirtilen yoldur.</span><span class="sxs-lookup"><span data-stu-id="73660-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="73660-159">Yolun bir parçası olarak eylem adını kullanmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="73660-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="73660-160">Her bir eylemin benzersiz ve belirsiz bir yol içerdiğinden emin olmanız yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="73660-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="73660-161">Yönlendirme öznitelikleri, belirli yolları oluşturmak için hem denetleyiciye hem de Yöntem düzeylerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="73660-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="73660-162">`List`Yöntemi, JSON olarak serileştirilmiş bir 200 Tamam yanıt kodu ve tüm Todo öğelerini döndürür.</span><span class="sxs-lookup"><span data-stu-id="73660-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="73660-163">Yeni API yönteminizi, [Postman](https://www.getpostman.com/docs/)gibi çeşitli araçlar kullanarak test edebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="73660-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Postman konsolu, todoıtems için bir GET isteği ve döndürülen üç öğe için JSON 'ı gösteren yanıtın gövdesini gösterir](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="73660-165">Öğe oluşturma</span><span class="sxs-lookup"><span data-stu-id="73660-165">Creating Items</span></span>

<span data-ttu-id="73660-166">Kurala göre, yeni veri öğeleri oluşturmak HTTP POST fiili ile eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="73660-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="73660-167">`Create`Yöntemine `[HttpPost]` uygulanan bir özniteliği vardır ve bir örneği kabul eder `ToDoItem` .</span><span class="sxs-lookup"><span data-stu-id="73660-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="73660-168">`item`Bağımsız DEĞIŞKEN gönderi gövdesinde geçirildiğinden, bu parametre `[FromBody]` özniteliğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="73660-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="73660-169">Yöntemi içinde, öğe geçerliliği için denetlenir ve veri deposunda daha önce bulunur ve herhangi bir sorun oluşursa, depo kullanılarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="73660-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="73660-170">`ModelState.IsValid` [Model doğrulaması](../mvc/models/validation.md)gerçekleştirir ve Kullanıcı girişini kabul eden her API yönteminde yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="73660-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="73660-171">Örnek, mobil istemciye geçirilen hata kodlarını içeren bir Enum kullanır:</span><span class="sxs-lookup"><span data-stu-id="73660-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="73660-172">İsteğin gövdesinde JSON biçiminde yeni nesne sağlayan POST fiilini kullanarak yeni öğe ekleme işlemini test edin.</span><span class="sxs-lookup"><span data-stu-id="73660-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="73660-173">Ayrıca, öğesini belirten bir istek üst bilgisi eklemeniz `Content-Type` gerekir `application/json` .</span><span class="sxs-lookup"><span data-stu-id="73660-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![GÖNDERI ve yanıt gösteren Postman konsolu](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="73660-175">Yöntemi, yanıtta yeni oluşturulan öğeyi döndürür.</span><span class="sxs-lookup"><span data-stu-id="73660-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="73660-176">Öğeler güncelleştiriliyor</span><span class="sxs-lookup"><span data-stu-id="73660-176">Updating Items</span></span>

<span data-ttu-id="73660-177">Kayıtları değiştirme HTTP PUT istekleri kullanılarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="73660-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="73660-178">Bu değişiklik dışında, `Edit` yöntemi neredeyse aynıdır `Create` .</span><span class="sxs-lookup"><span data-stu-id="73660-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="73660-179">Kayıt bulunamazsa, `Edit` eylem bir `NotFound` (404) yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="73660-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="73660-180">Postman ile test etmek için fiili ' i koymak için değiştirin.</span><span class="sxs-lookup"><span data-stu-id="73660-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="73660-181">İsteğin gövdesinde güncelleştirilmiş nesne verilerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="73660-181">Specify the updated object data in the Body of the request.</span></span>

![Bir PUT ve Response gösteren Postman konsolu](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="73660-183">Bu yöntem `NoContent` , başarılı olduğunda, önceden var olan API ile tutarlılık için (204) yanıtını döndürür.</span><span class="sxs-lookup"><span data-stu-id="73660-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="73660-184">Öğeleri silme</span><span class="sxs-lookup"><span data-stu-id="73660-184">Deleting Items</span></span>

<span data-ttu-id="73660-185">Kayıtları silme işlemi, hizmete SILME istekleri yapılarak ve silinecek öğenin KIMLIĞI geçirilerek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="73660-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="73660-186">Güncelleştirmelerde olduğu gibi, mevcut olmayan öğeler için istekler `NotFound` yanıt alır.</span><span class="sxs-lookup"><span data-stu-id="73660-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="73660-187">Aksi takdirde, başarılı bir istek `NoContent` (204) yanıtını alır.</span><span class="sxs-lookup"><span data-stu-id="73660-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="73660-188">Silme işlevinin test edilirken, isteğin gövdesinde hiçbir şey gerekmediği unutulmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="73660-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Bir DELETE ve Response gösteren Postman konsolu](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="73660-190">Ortak Web API kuralları</span><span class="sxs-lookup"><span data-stu-id="73660-190">Common Web API Conventions</span></span>

<span data-ttu-id="73660-191">Uygulamanız için arka uç hizmetlerini geliştirirken, çapraz kesme sorunlarını işlemeye yönelik tutarlı bir kural veya ilke kümesiyle birlikte çalışmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="73660-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="73660-192">Örneğin, yukarıda gösterilen hizmette, bulunamayan belirli kayıtlara yönelik istekler `NotFound` Yanıt yerine bir yanıt aldı `BadRequest` .</span><span class="sxs-lookup"><span data-stu-id="73660-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="73660-193">Benzer şekilde, bu hizmete, model bağlantılı türler 'e geçirilen komutlar her zaman denetlenir `ModelState.IsValid` ve `BadRequest` geçersiz model türleri için döndürülür.</span><span class="sxs-lookup"><span data-stu-id="73660-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="73660-194">API 'leriniz için ortak bir ilke tanımladıktan sonra, genellikle bunu bir [filtrede](../mvc/controllers/filters.md)kapsülleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="73660-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="73660-195">[ASP.NET Core MVC uygulamalarında ortak API ilkelerini kapsüllemek](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="73660-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73660-196">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="73660-196">Additional resources</span></span>

* [<span data-ttu-id="73660-197">Kimlik doğrulama ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="73660-197">Authentication and Authorization</span></span>](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
