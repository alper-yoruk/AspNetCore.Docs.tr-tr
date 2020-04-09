---
title: ASP.NET Core'dan web API'sını arayınBlazor
author: guardrex
description: JSON yardımcılarını kullanarak bir Blazor uygulamadan, kökenler arası kaynak paylaşımı (CORS) istekleri ni içeren bir web API'sini nasıl çağırabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660148"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="77342-103">ASP.NET Core'dan web API'sını arayınBlazor</span><span class="sxs-lookup"><span data-stu-id="77342-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="77342-104">Luke [Latham](https://github.com/guardrex)tarafından , [Daniel Roth](https://github.com/danroth27), ve Juan De la [Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="77342-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="77342-105">WebAssembly uygulamaları önceden yapılandırılmış bir `HttpClient` hizmeti kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="77342-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="77342-106">JSON yardımcılarını kullanarak Blazor veya JavaScript <xref:System.Net.Http.HttpRequestMessage>Getir [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçeneklerini içerebilen istekler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77342-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="77342-107">WebAssembly `HttpClient` Blazor uygulamalarındaki hizmet, istekleri menşe sunucusuna geri yapmaya odaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="77342-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="77342-108">Bu konudaki kılavuz yalnızca Blazor WebAssembly uygulamalarıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="77342-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="77342-109">Sunucu uygulamaları genellikle kullanılarak <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory>oluşturulan örnekleri kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="77342-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="77342-110">Bu konudaki kılavuz Sunucu uygulamalarıyla Blazor ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="77342-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="77342-111">Sunucu Blazor uygulamaları geliştirirken, 'deki <xref:fundamentals/http-requests>kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="77342-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="77342-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="77342-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="77342-113">*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:</span><span class="sxs-lookup"><span data-stu-id="77342-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="77342-114">Arama Web API (*Sayfalar / CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="77342-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="77342-115">HTTP İstek Tester (*Bileşenleri / HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="77342-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="77342-116">Paketler</span><span class="sxs-lookup"><span data-stu-id="77342-116">Packages</span></span>

<span data-ttu-id="77342-117">Referans *deneysel* [Microsoft.AspNetCore.Blazor. Proje](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) dosyasında httpClient NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="77342-117">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="77342-118">`Microsoft.AspNetCore.Blazor.HttpClient`ve `HttpClient` [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/)dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="77342-118">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="77342-119">Kararlı bir API kullanmak için, [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)kullanan [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) paketini kullanın.</span><span class="sxs-lookup"><span data-stu-id="77342-119">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="77342-120">Kararlı API'yi `Microsoft.AspNet.WebApi.Client` içinde kullanmak, deneysel `Microsoft.AspNetCore.Blazor.HttpClient` pakete özgü olan bu konuda açıklanan JSON yardımcılarını sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="77342-120">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="77342-121">Httpİsteve JSON yardımcıları</span><span class="sxs-lookup"><span data-stu-id="77342-121">HttpClient and JSON helpers</span></span>

<span data-ttu-id="77342-122">Bir Blazor WebAssembly [uygulamasında, HttpClient](xref:fundamentals/http-requests) istekleri kaynak sunucuya geri yapmak için önceden yapılandırılmış bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77342-122">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="77342-123">Sunucu Blazor uygulaması varsayılan olarak `HttpClient` bir hizmet içermez.</span><span class="sxs-lookup"><span data-stu-id="77342-123">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="77342-124">`HttpClient` [HttpClient fabrika altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="77342-124">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="77342-125">`HttpClient`ve JSON yardımcıları da üçüncü taraf web API uç noktaları aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77342-125">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="77342-126">`HttpClient`tarayıcı [Getir API](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesinin uygulanması da dahil olmak üzere kendi sınırlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="77342-126">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="77342-127">İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="77342-127">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="77342-128">Yönergeyi kullanarak bir `HttpClient` örnek enjekte edin: `@inject`</span><span class="sxs-lookup"><span data-stu-id="77342-128">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="77342-129">Aşağıdaki örneklerde, bir Todo web API işlemleri (CRUD) işlemleri oluşturur, okur, günceller ve siler.</span><span class="sxs-lookup"><span data-stu-id="77342-129">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="77342-130">Örnekler, aşağıdakileri `TodoItem` depolayan bir sınıfa dayanır:</span><span class="sxs-lookup"><span data-stu-id="77342-130">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="77342-131">Kimlik`Id`( `long` &ndash; , ) Öğenin benzersiz kimliği.</span><span class="sxs-lookup"><span data-stu-id="77342-131">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="77342-132">Ad`Name`( `string` &ndash; , ) Öğenin adı.</span><span class="sxs-lookup"><span data-stu-id="77342-132">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="77342-133">Durum`IsComplete`( `bool` &ndash; , ) Todo öğesinin bitip bitmeyişini belirtin.</span><span class="sxs-lookup"><span data-stu-id="77342-133">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="77342-134">JSON yardımcı yöntemleri bir URI'ye (aşağıdaki örneklerdeki bir web API) istek gönderir ve yanıtı işlemeye</span><span class="sxs-lookup"><span data-stu-id="77342-134">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="77342-135">`GetJsonAsync`&ndash; Bir HTTP GET isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="77342-135">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="77342-136">Aşağıdaki kodda, `_todoItems` bileşen tarafından görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="77342-136">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="77342-137">Yöntem, `GetTodoItems` bileşen işleme tamamlandığında tetiklenir ([OnInitializedAsync).](xref:blazor/lifecycle#component-initialization-methods)</span><span class="sxs-lookup"><span data-stu-id="77342-137">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="77342-138">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="77342-138">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="77342-139">`PostJsonAsync`&ndash; JSON kodlanmış içeriği içeren bir HTTP POST isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="77342-139">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="77342-140">Aşağıdaki kodda, `_newItemName` bileşenin bağlı bir öğesi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="77342-140">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="77342-141">Yöntem `AddItem` bir `<button>` öğe seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="77342-141">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="77342-142">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="77342-142">See the sample app for a complete example.</span></span>

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
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* <span data-ttu-id="77342-143">`PutJsonAsync`&ndash; JSON kodlanmış içerik de dahil olmak üzere bir HTTP PUT isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="77342-143">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="77342-144">Aşağıdaki kodda, `_editItem` bileşenin `IsCompleted` bağlı elemanları tarafından `Name` sağlanan değerler.</span><span class="sxs-lookup"><span data-stu-id="77342-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="77342-145">Öğe UI'nin `Id` başka bir bölümünde seçildiğinde ve `EditItem` çağrıldığında maddenin ki ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="77342-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="77342-146">Yöntem `SaveItem` Kaydet `<button>` öğesi seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="77342-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="77342-147">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="77342-147">See the sample app for a complete example.</span></span>

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
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="77342-148"><xref:System.Net.Http>HTTP isteklerini göndermek ve HTTP yanıtları almak için ek uzatma yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="77342-148"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="77342-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) bir WEB API'ya HTTP DELETE isteği göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77342-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="77342-150">Aşağıdaki kodda Sil `<button>` öğesi `DeleteItem` yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="77342-150">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="77342-151">Bağlı `<input>` öğe, `id` silmek için öğenin sağlar.</span><span class="sxs-lookup"><span data-stu-id="77342-151">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="77342-152">Tam bir örnek için örnek uygulamaya bakın.</span><span class="sxs-lookup"><span data-stu-id="77342-152">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="77342-153">Orijinler arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="77342-153">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="77342-154">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="77342-154">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="77342-155">Bu kısıtlamaya *aynı kaynak ilkesi*denir.</span><span class="sxs-lookup"><span data-stu-id="77342-155">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="77342-156">Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="77342-156">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="77342-157">Tarayıcıdan farklı bir başlangıç noktası olan bir bitiş noktasına istekte bulunmak *için, bitiş noktasının* [kökenler arası kaynak paylaşımını (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="77342-157">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="77342-158">WebAssembly örnek uygulaması (BlazorWebAssemblySample) Çağrı Web API bileşeninde CORS kullanımını gösterir (*Sayfalar / CallWebAPI.razor*). [ Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)</span><span class="sxs-lookup"><span data-stu-id="77342-158">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="77342-159">Diğer sitelerin uygulamanızda kökenler arası kaynak paylaşımı (CORS) <xref:security/cors>isteklerini yerine getirmelerine izin vermek için bkz.</span><span class="sxs-lookup"><span data-stu-id="77342-159">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="77342-160">ApI Getir istek seçenekleriyle HttpClient ve HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="77342-160">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="77342-161">WebAssembly uygulamasında çalışırken [HttpClient'ı](xref:fundamentals/http-requests) kullanın ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirin. Blazor</span><span class="sxs-lookup"><span data-stu-id="77342-161">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="77342-162">Örneğin, uri, HTTP yöntemi ve istenen istek üstbilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77342-162">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="77342-163">API Getir seçenekleri hakkında daha fazla bilgi için [BKZ: MDN web dokümanları: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="77342-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="77342-164">CORS isteklerine kimlik bilgileri (yetkilendirme çerezleri/üstbilgiler) gönderirken, üstbilginin `Authorization` CORS ilkesi tarafından izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="77342-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="77342-165">Aşağıdaki ilke aşağıdakiler için yapılandırmayı içerir:</span><span class="sxs-lookup"><span data-stu-id="77342-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="77342-166">İstek kökenleri`http://localhost:5000` `https://localhost:5001`( , ).</span><span class="sxs-lookup"><span data-stu-id="77342-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="77342-167">Herhangi bir yöntem (fiil).</span><span class="sxs-lookup"><span data-stu-id="77342-167">Any method (verb).</span></span>
* <span data-ttu-id="77342-168">`Content-Type`ve `Authorization` üstbilgi.</span><span class="sxs-lookup"><span data-stu-id="77342-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="77342-169">Özel bir üstbilgi (örneğin, `x-custom-header`), ararken üstbilgi listelemek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="77342-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="77342-170">İstemci tarafı JavaScript koduna göre ayarlanan kimlik bilgileri (özellik`credentials` olarak `include`ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="77342-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="77342-171">Daha fazla bilgi <xref:security/cors> için, bkz ve örnek uygulamanın HTTP İstek Tester bileşeni *(Components/HTTPRequestTester.razor).*</span><span class="sxs-lookup"><span data-stu-id="77342-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77342-172">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77342-172">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="77342-173">Kerkenez HTTPS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="77342-173">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="77342-174">W3C'de Çapraz Köken Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="77342-174">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
