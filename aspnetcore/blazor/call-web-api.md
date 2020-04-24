---
title: ASP.NET Core Blazor webassembly 'ten BIR Web API 'si çağırma
author: guardrex
description: Çıkış noktaları arası kaynak paylaşımı (CORS) istekleri Blazor yapma dahil olmak üzere, JSON yardımcıları kullanarak webassembly uygulamasından BIR Web API 'si çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122227"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="f03e7-103">ASP.NET Core Blazor 'ten bir Web API 'SI çağırma</span><span class="sxs-lookup"><span data-stu-id="f03e7-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="f03e7-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından</span><span class="sxs-lookup"><span data-stu-id="f03e7-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f03e7-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Apps önceden yapılandırılmış `HttpClient` bir hizmeti kullanarak Web API 'lerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="f03e7-106">Blazor JSON yardımcıları veya ile <xref:System.Net.Http.HttpRequestMessage>kullanarak JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçeneklerini içerebilen oluşturma istekleri.</span><span class="sxs-lookup"><span data-stu-id="f03e7-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="f03e7-107">Blazor `HttpClient` WebAssembly Apps 'teki hizmet, isteklerin kaynak sunucusuna geri getirilmesi üzerine odaklanır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="f03e7-108">Bu konudaki kılavuz yalnızca Blazor WebAssembly Apps ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="f03e7-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) Apps, genellikle kullanılarak <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory>oluşturulan örnekleri kullanarak Web API 'lerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="f03e7-110">Bu konudaki kılavuz Blazor Server uygulamalarıyla ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="f03e7-111">Blazor Server uygulamaları geliştirirken, içindeki <xref:fundamentals/http-requests>yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="f03e7-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="f03e7-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="f03e7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="f03e7-113">*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:</span><span class="sxs-lookup"><span data-stu-id="f03e7-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="f03e7-114">Web API çağrısı (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="f03e7-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="f03e7-115">HTTP Isteği Sınayıcısı (*Bileşenler/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="f03e7-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="f03e7-116">Paketler</span><span class="sxs-lookup"><span data-stu-id="f03e7-116">Packages</span></span>

<span data-ttu-id="f03e7-117">Proje dosyasındaki [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="f03e7-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="f03e7-118">HttpClient hizmetini ekleme</span><span class="sxs-lookup"><span data-stu-id="f03e7-118">Add the HttpClient service</span></span>

<span data-ttu-id="f03e7-119">İçinde `Program.Main`, zaten yoksa `HttpClient` bir hizmet ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f03e7-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="f03e7-120">HttpClient ve JSON yardımcıları</span><span class="sxs-lookup"><span data-stu-id="f03e7-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="f03e7-121">Blazor WebAssembly uygulamasında [HttpClient](xref:fundamentals/http-requests) , istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="f03e7-122">Bir Blazor sunucu uygulaması varsayılan olarak bir `HttpClient` hizmet içermez.</span><span class="sxs-lookup"><span data-stu-id="f03e7-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="f03e7-123">`HttpClient` [HttpClient Factory altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir uygulama sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f03e7-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="f03e7-124">`HttpClient`ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="f03e7-125">`HttpClient`, tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="f03e7-126">İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="f03e7-127">`@inject` Yönergesini kullanarak bir `HttpClient` örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="f03e7-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="f03e7-128">Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler.</span><span class="sxs-lookup"><span data-stu-id="f03e7-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="f03e7-129">Örnekler şunları depolayan bir `TodoItem` sınıfa dayalıdır:</span><span class="sxs-lookup"><span data-stu-id="f03e7-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="f03e7-130">Öğenin kimliği`Id`( `long`, &ndash; ) benzersiz kimliği.</span><span class="sxs-lookup"><span data-stu-id="f03e7-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="f03e7-131">Öğenin adı`Name`( `string`, &ndash; ).</span><span class="sxs-lookup"><span data-stu-id="f03e7-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="f03e7-132">Todo öğesi`IsComplete`tamamlandığında `bool`durum &ndash; (,) göstergesi.</span><span class="sxs-lookup"><span data-stu-id="f03e7-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="f03e7-133">JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:</span><span class="sxs-lookup"><span data-stu-id="f03e7-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="f03e7-134">`GetFromJsonAsync`&ndash; BIR http get isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="f03e7-135">Aşağıdaki kodda,, `_todoItems` bileşeni tarafından görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="f03e7-136">`GetTodoItems` Yöntemi, bileşen işlemeyi tamamladığında tetiklenir ([Onınitializedadsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="f03e7-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="f03e7-137">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="f03e7-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="f03e7-138">`PostAsJsonAsync`&ndash; JSON kodlu içerik dahil olmak üzere BIR http post isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="f03e7-139">Aşağıdaki kodda, `_newItemName` bileşenin bağlantılı bir öğesi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="f03e7-140">`AddItem` Yöntemi bir `<button>` öğesi seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="f03e7-141">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="f03e7-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="f03e7-142">`PostAsJsonAsync` Döndürecek çağrılar <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="f03e7-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="f03e7-143">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f03e7-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="f03e7-144">`PutAsJsonAsync`&ndash; JSON kodlu içerik dahil olmak üzere BIR http put isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="f03e7-145">Aşağıdaki kodda, `_editItem` `Name` ve `IsCompleted` değerleri bileşenin bağlantılı öğelerine göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="f03e7-146">`Id` Öğe, Kullanıcı arabiriminin başka bir bölümünde seçildiğinde ayarlanır ve `EditItem` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="f03e7-147">`SaveItem` Yöntemi, Kaydet `<button>` öğesi seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="f03e7-148">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="f03e7-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  <span data-ttu-id="f03e7-149">`PutAsJsonAsync` Döndürecek çağrılar <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="f03e7-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="f03e7-150">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f03e7-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="f03e7-151"><xref:System.Net.Http>HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="f03e7-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) , BIR Web API 'SINE http delete isteği göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="f03e7-153">Aşağıdaki kodda, Delete `<button>` öğesi `DeleteItem` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="f03e7-154">Bağlantılı `<input>` öğe, silinecek öğenin `id` bir listesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f03e7-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="f03e7-155">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="f03e7-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="f03e7-156">Çıkış noktaları arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="f03e7-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="f03e7-157">Tarayıcı güvenliği, bir Web sayfasının, Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="f03e7-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="f03e7-158">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="f03e7-159">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="f03e7-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="f03e7-160">Tarayıcıdan farklı bir kaynağa sahip bir uç noktaya istek yapmak için *uç noktanın* , [çıkış noktaları arası kaynak PAYLAŞıMı 'nı (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="f03e7-161">[Blazor WebAssembly örnek uygulaması (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , Web API bileşeni ÇAĞıRMA bileşeninde CORS 'nin kullanımını gösterir (*Pages/callwebapi. Razor*).</span><span class="sxs-lookup"><span data-stu-id="f03e7-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="f03e7-162">Diğer sitelerin uygulamanıza çıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapmasına izin vermek için bkz <xref:security/cors>..</span><span class="sxs-lookup"><span data-stu-id="f03e7-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="f03e7-163">API istek seçeneklerini getiren HttpClient ve HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="f03e7-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="f03e7-164">Blazor WebAssembly uygulamasında WebAssembly üzerinde çalışırken, [HttpClient](xref:fundamentals/http-requests) kullanın ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f03e7-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="f03e7-165">Örneğin, istek URI 'SI, HTTP yöntemi ve istenen istek üst bilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f03e7-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="f03e7-166">.NET WebAssembly 'ın uygulanması, `HttpClient` [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-166">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="f03e7-167">Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f03e7-167">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="f03e7-168">HTTP getirme isteği seçenekleri, aşağıdaki tabloda gösterilen `HttpRequestMessage` uzantı yöntemleriyle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-168">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="f03e7-169">`HttpRequestMessage`genişletme yöntemi</span><span class="sxs-lookup"><span data-stu-id="f03e7-169">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="f03e7-170">Fetch isteği özelliği</span><span class="sxs-lookup"><span data-stu-id="f03e7-170">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="f03e7-171">Credentials</span><span class="sxs-lookup"><span data-stu-id="f03e7-171">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="f03e7-172">önbellek</span><span class="sxs-lookup"><span data-stu-id="f03e7-172">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="f03e7-173">modundaysa</span><span class="sxs-lookup"><span data-stu-id="f03e7-173">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="f03e7-174">doğruluğunu</span><span class="sxs-lookup"><span data-stu-id="f03e7-174">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="f03e7-175">Daha genel `SetBrowserRequestOption` genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f03e7-175">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="f03e7-176">HTTP yanıtı, yanıt içeriğindeki eşitleme okuma desteğini Blazor etkinleştirmek için genellikle webassembly uygulamasında arabelleğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="f03e7-177">Yanıt akışı desteğini etkinleştirmek için istekteki `SetBrowserResponseStreamingEnabled` genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f03e7-177">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="f03e7-178">Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, `SetBrowserRequestCredentials` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f03e7-178">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="f03e7-179">API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="f03e7-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="f03e7-180">CORS isteklerindeki kimlik bilgileri (yetkilendirme tanımlama bilgileri/üstbilgileri) gönderilirken, `Authorization` bir üst bilgiye CORS ilkesi tarafından izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="f03e7-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="f03e7-181">Aşağıdaki ilke için yapılandırma içerir:</span><span class="sxs-lookup"><span data-stu-id="f03e7-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="f03e7-182">İstek kaynakları (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="f03e7-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="f03e7-183">Any yöntemi (fiil).</span><span class="sxs-lookup"><span data-stu-id="f03e7-183">Any method (verb).</span></span>
* <span data-ttu-id="f03e7-184">`Content-Type`ve `Authorization` üst bilgiler.</span><span class="sxs-lookup"><span data-stu-id="f03e7-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="f03e7-185">Özel bir üstbilgiye (örneğin, `x-custom-header`) izin vermek için, çağrılırken <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>üstbilgiyi listeleyin.</span><span class="sxs-lookup"><span data-stu-id="f03e7-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="f03e7-186">İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri (`credentials` özellik olarak `include`ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="f03e7-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="f03e7-187">Daha fazla bilgi için, <xref:security/cors> bkz. ve örnek uygulamanın http isteği Sınayıcısı bileşeni (*Bileşenler/httprequesttester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="f03e7-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f03e7-188">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f03e7-188">Additional resources</span></span>

* [<span data-ttu-id="f03e7-189">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="f03e7-189">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f03e7-190">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="f03e7-190">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="f03e7-191">Kestrel HTTPS uç noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f03e7-191">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="f03e7-192">W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="f03e7-192">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
