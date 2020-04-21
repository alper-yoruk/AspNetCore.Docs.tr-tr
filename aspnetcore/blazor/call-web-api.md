---
title: Core Blazor WebAssembly'den web APIASP.NET
author: guardrex
description: JSON yardımcılarını kullanarak bir Blazor WebAssembly uygulamasından ,orijinler arası kaynak paylaşımı (CORS) isteklerini oluşturma dahil olmak üzere web API'sını nasıl arayacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 943f9d440adbe11ac1977f28aebee53a5510a86b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661575"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="359b2-103">ASP.NET Core'dan web API'sını arayınBlazor</span><span class="sxs-lookup"><span data-stu-id="359b2-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="359b2-104">Luke [Latham](https://github.com/guardrex)tarafından , [Daniel Roth](https://github.com/danroth27), ve Juan De la [Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="359b2-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="359b2-105">WebAssembly uygulamaları önceden yapılandırılmış bir `HttpClient` hizmeti kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="359b2-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="359b2-106">JSON yardımcılarını kullanarak Blazor veya JavaScript <xref:System.Net.Http.HttpRequestMessage>Getir [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçeneklerini içerebilen istekler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="359b2-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="359b2-107">WebAssembly `HttpClient` Blazor uygulamalarındaki hizmet, istekleri menşe sunucusuna geri yapmaya odaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="359b2-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="359b2-108">Bu konudaki kılavuz yalnızca Blazor WebAssembly uygulamalarıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="359b2-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="359b2-109">Sunucu uygulamaları genellikle kullanılarak <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory>oluşturulan örnekleri kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="359b2-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="359b2-110">Bu konudaki kılavuz Sunucu uygulamalarıyla Blazor ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="359b2-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="359b2-111">Sunucu Blazor uygulamaları geliştirirken, 'deki <xref:fundamentals/http-requests>kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="359b2-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="359b2-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="359b2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="359b2-113">*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:</span><span class="sxs-lookup"><span data-stu-id="359b2-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="359b2-114">Arama Web API (*Sayfalar / CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="359b2-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="359b2-115">HTTP İstek Tester (*Bileşenleri / HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="359b2-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="359b2-116">Paketler</span><span class="sxs-lookup"><span data-stu-id="359b2-116">Packages</span></span>

<span data-ttu-id="359b2-117">Proje dosyasındaki [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="359b2-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="359b2-118">HttpClient hizmetini ekleme</span><span class="sxs-lookup"><span data-stu-id="359b2-118">Add the HttpClient service</span></span>

<span data-ttu-id="359b2-119">In, `Program.Main`zaten `HttpClient` yoksa bir hizmet ekleyin:</span><span class="sxs-lookup"><span data-stu-id="359b2-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="359b2-120">Httpİsteve JSON yardımcıları</span><span class="sxs-lookup"><span data-stu-id="359b2-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="359b2-121">Bir Blazor WebAssembly [uygulamasında, HttpClient](xref:fundamentals/http-requests) istekleri kaynak sunucuya geri yapmak için önceden yapılandırılmış bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="359b2-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="359b2-122">Sunucu Blazor uygulaması varsayılan olarak `HttpClient` bir hizmet içermez.</span><span class="sxs-lookup"><span data-stu-id="359b2-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="359b2-123">`HttpClient` [HttpClient fabrika altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="359b2-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="359b2-124">`HttpClient`ve JSON yardımcıları da üçüncü taraf web API uç noktaları aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="359b2-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="359b2-125">`HttpClient`tarayıcı [Getir API](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesinin uygulanması da dahil olmak üzere kendi sınırlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="359b2-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="359b2-126">İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="359b2-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="359b2-127">Yönergeyi kullanarak bir `HttpClient` örnek enjekte edin: `@inject`</span><span class="sxs-lookup"><span data-stu-id="359b2-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="359b2-128">Aşağıdaki örneklerde, bir Todo web API işlemleri (CRUD) işlemleri oluşturur, okur, günceller ve siler.</span><span class="sxs-lookup"><span data-stu-id="359b2-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="359b2-129">Örnekler, aşağıdakileri `TodoItem` depolayan bir sınıfa dayanır:</span><span class="sxs-lookup"><span data-stu-id="359b2-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="359b2-130">Kimlik`Id`( `long` &ndash; , ) Öğenin benzersiz kimliği.</span><span class="sxs-lookup"><span data-stu-id="359b2-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="359b2-131">Ad`Name`( `string` &ndash; , ) Öğenin adı.</span><span class="sxs-lookup"><span data-stu-id="359b2-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="359b2-132">Durum`IsComplete`( `bool` &ndash; , ) Todo öğesinin bitip bitmeyişini belirtin.</span><span class="sxs-lookup"><span data-stu-id="359b2-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="359b2-133">JSON yardımcı yöntemleri bir URI'ye (aşağıdaki örneklerdeki bir web API) istek gönderir ve yanıtı işlemeye</span><span class="sxs-lookup"><span data-stu-id="359b2-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="359b2-134">`GetFromJsonAsync`&ndash; Bir HTTP GET isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="359b2-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="359b2-135">Aşağıdaki kodda, `_todoItems` bileşen tarafından görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="359b2-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="359b2-136">Yöntem, `GetTodoItems` bileşen işleme tamamlandığında tetiklenir ([OnInitializedAsync).](xref:blazor/lifecycle#component-initialization-methods)</span><span class="sxs-lookup"><span data-stu-id="359b2-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="359b2-137">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="359b2-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="359b2-138">`PostAsJsonAsync`&ndash; JSON kodlanmış içeriği içeren bir HTTP POST isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="359b2-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="359b2-139">Aşağıdaki kodda, `_newItemName` bileşenin bağlı bir öğesi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="359b2-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="359b2-140">Yöntem `AddItem` bir `<button>` öğe seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="359b2-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="359b2-141">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="359b2-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="359b2-142">`PostAsJsonAsync` Bir <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="359b2-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="359b2-143">Yanıt iletisinden JSON içeriğini deserialize etmek `ReadFromJsonAsync<T>` için uzantı yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="359b2-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="359b2-144">`PutAsJsonAsync`&ndash; JSON kodlanmış içerik de dahil olmak üzere bir HTTP PUT isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="359b2-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="359b2-145">Aşağıdaki kodda, `_editItem` bileşenin `IsCompleted` bağlı elemanları tarafından `Name` sağlanan değerler.</span><span class="sxs-lookup"><span data-stu-id="359b2-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="359b2-146">Öğe UI'nin `Id` başka bir bölümünde seçildiğinde ve `EditItem` çağrıldığında maddenin ki ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="359b2-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="359b2-147">Yöntem `SaveItem` Kaydet `<button>` öğesi seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="359b2-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="359b2-148">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="359b2-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="359b2-149">`PutAsJsonAsync` Bir <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="359b2-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="359b2-150">Yanıt iletisinden JSON içeriğini deserialize etmek `ReadFromJsonAsync<T>` için uzantı yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="359b2-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="359b2-151"><xref:System.Net.Http>HTTP isteklerini göndermek ve HTTP yanıtları almak için ek uzatma yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="359b2-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="359b2-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) bir WEB API'ya HTTP DELETE isteği göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="359b2-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="359b2-153">Aşağıdaki kodda Sil `<button>` öğesi `DeleteItem` yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="359b2-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="359b2-154">Bağlı `<input>` öğe, `id` silmek için öğenin sağlar.</span><span class="sxs-lookup"><span data-stu-id="359b2-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="359b2-155">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="359b2-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="359b2-156">Orijinler arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="359b2-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="359b2-157">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="359b2-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="359b2-158">Bu kısıtlamaya *aynı kaynak ilkesi*denir.</span><span class="sxs-lookup"><span data-stu-id="359b2-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="359b2-159">Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="359b2-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="359b2-160">Tarayıcıdan farklı bir başlangıç noktası olan bir bitiş noktasına istekte bulunmak *için, bitiş noktasının* [kökenler arası kaynak paylaşımını (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="359b2-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="359b2-161">WebAssembly örnek uygulaması (BlazorWebAssemblySample) Çağrı Web API bileşeninde CORS kullanımını gösterir (*Sayfalar / CallWebAPI.razor*). [ Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)</span><span class="sxs-lookup"><span data-stu-id="359b2-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="359b2-162">Diğer sitelerin uygulamanızda kökenler arası kaynak paylaşımı (CORS) <xref:security/cors>isteklerini yerine getirmelerine izin vermek için bkz.</span><span class="sxs-lookup"><span data-stu-id="359b2-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="359b2-163">ApI Getir istek seçenekleriyle HttpClient ve HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="359b2-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="359b2-164">WebAssembly uygulamasında çalışırken [HttpClient'ı](xref:fundamentals/http-requests) kullanın ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirin. Blazor</span><span class="sxs-lookup"><span data-stu-id="359b2-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="359b2-165">Örneğin, uri, HTTP yöntemi ve istenen istek üstbilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="359b2-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="359b2-166">API Getir seçenekleri hakkında daha fazla bilgi için [BKZ: MDN web dokümanları: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="359b2-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="359b2-167">CORS isteklerine kimlik bilgileri (yetkilendirme çerezleri/üstbilgiler) gönderirken, üstbilginin `Authorization` CORS ilkesi tarafından izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="359b2-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="359b2-168">Aşağıdaki ilke aşağıdakiler için yapılandırmayı içerir:</span><span class="sxs-lookup"><span data-stu-id="359b2-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="359b2-169">İstek kökenleri`http://localhost:5000` `https://localhost:5001`( , ).</span><span class="sxs-lookup"><span data-stu-id="359b2-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="359b2-170">Herhangi bir yöntem (fiil).</span><span class="sxs-lookup"><span data-stu-id="359b2-170">Any method (verb).</span></span>
* <span data-ttu-id="359b2-171">`Content-Type`ve `Authorization` üstbilgi.</span><span class="sxs-lookup"><span data-stu-id="359b2-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="359b2-172">Özel bir üstbilgi (örneğin, `x-custom-header`), ararken üstbilgi listelemek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="359b2-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="359b2-173">İstemci tarafı JavaScript koduna göre ayarlanan kimlik bilgileri (özellik`credentials` olarak `include`ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="359b2-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="359b2-174">Daha fazla bilgi <xref:security/cors> için, bkz ve örnek uygulamanın HTTP İstek Tester bileşeni *(Components/HTTPRequestTester.razor).*</span><span class="sxs-lookup"><span data-stu-id="359b2-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="359b2-175">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="359b2-175">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="359b2-176">Kerkenez HTTPS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="359b2-176">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="359b2-177">W3C'de Çapraz Köken Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="359b2-177">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
