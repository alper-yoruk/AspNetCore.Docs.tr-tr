---
title: Core Blazor WebAssembly'den web APIASP.NET
author: guardrex
description: JSON yardımcılarını kullanarak bir Blazor WebAssembly uygulamasından ,orijinler arası kaynak paylaşımı (CORS) isteklerini oluşturma dahil olmak üzere web API'sını nasıl arayacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 2f2d4150f4fa1e7f47310f2a88b816f445cd1d3a
ms.sourcegitcommit: 49c91ad4b69f4f8032394cbf2d5ae1b19a7f863b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81544862"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="234f1-103">ASP.NET Core'dan web API'sını arayınBlazor</span><span class="sxs-lookup"><span data-stu-id="234f1-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="234f1-104">Luke [Latham](https://github.com/guardrex)tarafından , [Daniel Roth](https://github.com/danroth27), ve Juan De la [Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="234f1-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="234f1-105">WebAssembly uygulamaları önceden yapılandırılmış bir `HttpClient` hizmeti kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="234f1-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="234f1-106">JSON yardımcılarını kullanarak Blazor veya JavaScript <xref:System.Net.Http.HttpRequestMessage>Getir [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçeneklerini içerebilen istekler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="234f1-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="234f1-107">WebAssembly `HttpClient` Blazor uygulamalarındaki hizmet, istekleri menşe sunucusuna geri yapmaya odaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="234f1-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="234f1-108">Bu konudaki kılavuz yalnızca Blazor WebAssembly uygulamalarıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="234f1-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="234f1-109">Sunucu uygulamaları genellikle kullanılarak <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory>oluşturulan örnekleri kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="234f1-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="234f1-110">Bu konudaki kılavuz Sunucu uygulamalarıyla Blazor ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="234f1-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="234f1-111">Sunucu Blazor uygulamaları geliştirirken, 'deki <xref:fundamentals/http-requests>kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="234f1-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="234f1-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="234f1-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="234f1-113">*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:</span><span class="sxs-lookup"><span data-stu-id="234f1-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="234f1-114">Arama Web API (*Sayfalar / CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="234f1-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="234f1-115">HTTP İstek Tester (*Bileşenleri / HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="234f1-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="234f1-116">Paketler</span><span class="sxs-lookup"><span data-stu-id="234f1-116">Packages</span></span>

<span data-ttu-id="234f1-117">Proje dosyasındaki [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="234f1-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="234f1-118">HttpClient hizmetini ekleme</span><span class="sxs-lookup"><span data-stu-id="234f1-118">Add the HttpClient service</span></span>

<span data-ttu-id="234f1-119">In, `Program.Main`zaten `HttpClient` yoksa bir hizmet ekleyin:</span><span class="sxs-lookup"><span data-stu-id="234f1-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="234f1-120">Httpİsteve JSON yardımcıları</span><span class="sxs-lookup"><span data-stu-id="234f1-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="234f1-121">Bir Blazor WebAssembly [uygulamasında, HttpClient](xref:fundamentals/http-requests) istekleri kaynak sunucuya geri yapmak için önceden yapılandırılmış bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="234f1-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="234f1-122">Sunucu Blazor uygulaması varsayılan olarak `HttpClient` bir hizmet içermez.</span><span class="sxs-lookup"><span data-stu-id="234f1-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="234f1-123">`HttpClient` [HttpClient fabrika altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="234f1-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="234f1-124">`HttpClient`ve JSON yardımcıları da üçüncü taraf web API uç noktaları aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="234f1-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="234f1-125">`HttpClient`tarayıcı [Getir API](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesinin uygulanması da dahil olmak üzere kendi sınırlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="234f1-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="234f1-126">İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="234f1-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="234f1-127">Yönergeyi kullanarak bir `HttpClient` örnek enjekte edin: `@inject`</span><span class="sxs-lookup"><span data-stu-id="234f1-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="234f1-128">Aşağıdaki örneklerde, bir Todo web API işlemleri (CRUD) işlemleri oluşturur, okur, günceller ve siler.</span><span class="sxs-lookup"><span data-stu-id="234f1-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="234f1-129">Örnekler, aşağıdakileri `TodoItem` depolayan bir sınıfa dayanır:</span><span class="sxs-lookup"><span data-stu-id="234f1-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="234f1-130">Kimlik`Id`( `long` &ndash; , ) Öğenin benzersiz kimliği.</span><span class="sxs-lookup"><span data-stu-id="234f1-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="234f1-131">Ad`Name`( `string` &ndash; , ) Öğenin adı.</span><span class="sxs-lookup"><span data-stu-id="234f1-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="234f1-132">Durum`IsComplete`( `bool` &ndash; , ) Todo öğesinin bitip bitmeyişini belirtin.</span><span class="sxs-lookup"><span data-stu-id="234f1-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="234f1-133">JSON yardımcı yöntemleri bir URI'ye (aşağıdaki örneklerdeki bir web API) istek gönderir ve yanıtı işlemeye</span><span class="sxs-lookup"><span data-stu-id="234f1-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="234f1-134">`GetFromJsonAsync`&ndash; Bir HTTP GET isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="234f1-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="234f1-135">Aşağıdaki kodda, `_todoItems` bileşen tarafından görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="234f1-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="234f1-136">Yöntem, `GetTodoItems` bileşen işleme tamamlandığında tetiklenir ([OnInitializedAsync).](xref:blazor/lifecycle#component-initialization-methods)</span><span class="sxs-lookup"><span data-stu-id="234f1-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="234f1-137">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="234f1-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="234f1-138">`PostAsJsonAsync`&ndash; JSON kodlanmış içeriği içeren bir HTTP POST isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="234f1-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="234f1-139">Aşağıdaki kodda, `_newItemName` bileşenin bağlı bir öğesi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="234f1-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="234f1-140">Yöntem `AddItem` bir `<button>` öğe seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="234f1-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="234f1-141">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="234f1-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="234f1-142">`PostAsJsonAsync` Bir <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="234f1-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span>

* <span data-ttu-id="234f1-143">`PutAsJsonAsync`&ndash; JSON kodlanmış içerik de dahil olmak üzere bir HTTP PUT isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="234f1-143">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="234f1-144">Aşağıdaki kodda, `_editItem` bileşenin `IsCompleted` bağlı elemanları tarafından `Name` sağlanan değerler.</span><span class="sxs-lookup"><span data-stu-id="234f1-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="234f1-145">Öğe UI'nin `Id` başka bir bölümünde seçildiğinde ve `EditItem` çağrıldığında maddenin ki ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="234f1-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="234f1-146">Yöntem `SaveItem` Kaydet `<button>` öğesi seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="234f1-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="234f1-147">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="234f1-147">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="234f1-148">`PutAsJsonAsync` Bir <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="234f1-148">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span>

<span data-ttu-id="234f1-149"><xref:System.Net.Http>HTTP isteklerini göndermek ve HTTP yanıtları almak için ek uzatma yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="234f1-149"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="234f1-150">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) bir WEB API'ya HTTP DELETE isteği göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="234f1-150">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="234f1-151">Aşağıdaki kodda Sil `<button>` öğesi `DeleteItem` yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="234f1-151">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="234f1-152">Bağlı `<input>` öğe, `id` silmek için öğenin sağlar.</span><span class="sxs-lookup"><span data-stu-id="234f1-152">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="234f1-153">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="234f1-153">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="234f1-154">Orijinler arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="234f1-154">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="234f1-155">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="234f1-155">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="234f1-156">Bu kısıtlamaya *aynı kaynak ilkesi*denir.</span><span class="sxs-lookup"><span data-stu-id="234f1-156">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="234f1-157">Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="234f1-157">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="234f1-158">Tarayıcıdan farklı bir başlangıç noktası olan bir bitiş noktasına istekte bulunmak *için, bitiş noktasının* [kökenler arası kaynak paylaşımını (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="234f1-158">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="234f1-159">WebAssembly örnek uygulaması (BlazorWebAssemblySample) Çağrı Web API bileşeninde CORS kullanımını gösterir (*Sayfalar / CallWebAPI.razor*). [ Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)</span><span class="sxs-lookup"><span data-stu-id="234f1-159">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="234f1-160">Diğer sitelerin uygulamanızda kökenler arası kaynak paylaşımı (CORS) <xref:security/cors>isteklerini yerine getirmelerine izin vermek için bkz.</span><span class="sxs-lookup"><span data-stu-id="234f1-160">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="234f1-161">ApI Getir istek seçenekleriyle HttpClient ve HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="234f1-161">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="234f1-162">WebAssembly uygulamasında çalışırken [HttpClient'ı](xref:fundamentals/http-requests) kullanın ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirin. Blazor</span><span class="sxs-lookup"><span data-stu-id="234f1-162">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="234f1-163">Örneğin, uri, HTTP yöntemi ve istenen istek üstbilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="234f1-163">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="234f1-164">API Getir seçenekleri hakkında daha fazla bilgi için [BKZ: MDN web dokümanları: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="234f1-164">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="234f1-165">CORS isteklerine kimlik bilgileri (yetkilendirme çerezleri/üstbilgiler) gönderirken, üstbilginin `Authorization` CORS ilkesi tarafından izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="234f1-165">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="234f1-166">Aşağıdaki ilke aşağıdakiler için yapılandırmayı içerir:</span><span class="sxs-lookup"><span data-stu-id="234f1-166">The following policy includes configuration for:</span></span>

* <span data-ttu-id="234f1-167">İstek kökenleri`http://localhost:5000` `https://localhost:5001`( , ).</span><span class="sxs-lookup"><span data-stu-id="234f1-167">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="234f1-168">Herhangi bir yöntem (fiil).</span><span class="sxs-lookup"><span data-stu-id="234f1-168">Any method (verb).</span></span>
* <span data-ttu-id="234f1-169">`Content-Type`ve `Authorization` üstbilgi.</span><span class="sxs-lookup"><span data-stu-id="234f1-169">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="234f1-170">Özel bir üstbilgi (örneğin, `x-custom-header`), ararken üstbilgi listelemek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="234f1-170">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="234f1-171">İstemci tarafı JavaScript koduna göre ayarlanan kimlik bilgileri (özellik`credentials` olarak `include`ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="234f1-171">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="234f1-172">Daha fazla bilgi <xref:security/cors> için, bkz ve örnek uygulamanın HTTP İstek Tester bileşeni *(Components/HTTPRequestTester.razor).*</span><span class="sxs-lookup"><span data-stu-id="234f1-172">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="234f1-173">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="234f1-173">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="234f1-174">Kerkenez HTTPS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="234f1-174">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="234f1-175">W3C'de Çapraz Köken Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="234f1-175">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
