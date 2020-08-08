---
title: ASP.NET Core bir Web API 'SI çağırmaBlazor WebAssembly
author: guardrex
description: Blazor WebAssemblyÇıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapma dahil olmak üzere JSON yardımcıları kullanarak bir Web API 'sini nasıl çağıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: ef31d3d9b3914f3c86aa397ff214778fe295964b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012598"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a><span data-ttu-id="370d7-103">ASP.NET Core bir Web API 'SI çağırmaBlazor</span><span class="sxs-lookup"><span data-stu-id="370d7-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="370d7-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından</span><span class="sxs-lookup"><span data-stu-id="370d7-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="370d7-105">Bu konu için geçerlidir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="370d7-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="370d7-106">[Blazor Server](xref:blazor/hosting-models#blazor-server)uygulamalar <xref:System.Net.Http.HttpClient> , genellikle kullanılarak oluşturulan örnekleri kullanarak Web API 'lerini çağırır <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="370d7-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="370d7-107">İçin geçerli olan rehberlik için Blazor Server bkz <xref:fundamentals/http-requests> ..</span><span class="sxs-lookup"><span data-stu-id="370d7-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="370d7-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)uygulamalar önceden yapılandırılmış bir hizmeti kullanarak Web API 'Lerini çağırır <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="370d7-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="370d7-109">JSON yardımcıları veya ile birlikte JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçenekleri içerebilen oluşturma istekleri Blazor <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="370d7-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="370d7-110"><xref:System.Net.Http.HttpClient>Uygulamalardaki hizmet, Blazor WebAssembly isteklerin kaynak sunucusuna geri getirilmesi üzerine odaklanır.</span><span class="sxs-lookup"><span data-stu-id="370d7-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="370d7-111">Bu konudaki kılavuz yalnızca uygulamalar için geçerlidir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="370d7-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="370d7-112">[Örnek kodu görüntüle veya indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)): `BlazorWebAssemblySample` uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="370d7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="370d7-113">Örnek uygulamada aşağıdaki bileşenlere bakın `BlazorWebAssemblySample` :</span><span class="sxs-lookup"><span data-stu-id="370d7-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="370d7-114">Web API ( `Pages/CallWebAPI.razor` ) çağrısı</span><span class="sxs-lookup"><span data-stu-id="370d7-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="370d7-115">HTTP Istek Sınayıcısı ( `Components/HTTPRequestTester.razor` )</span><span class="sxs-lookup"><span data-stu-id="370d7-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="370d7-116">Paketler</span><span class="sxs-lookup"><span data-stu-id="370d7-116">Packages</span></span>

<span data-ttu-id="370d7-117">[`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/)Proje dosyasındaki NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="370d7-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="370d7-118">HttpClient hizmetini ekleme</span><span class="sxs-lookup"><span data-stu-id="370d7-118">Add the HttpClient service</span></span>

<span data-ttu-id="370d7-119">İçinde `Program.Main` , <xref:System.Net.Http.HttpClient> zaten yoksa bir hizmet ekleyin:</span><span class="sxs-lookup"><span data-stu-id="370d7-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="370d7-120">HttpClient ve JSON yardımcıları</span><span class="sxs-lookup"><span data-stu-id="370d7-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="370d7-121">Bir Blazor WebAssembly uygulamada, [`HttpClient`](xref:fundamentals/http-requests) istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="370d7-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="370d7-122">Bir Blazor Server uygulama <xref:System.Net.Http.HttpClient> Varsayılan olarak bir hizmet içermez.</span><span class="sxs-lookup"><span data-stu-id="370d7-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="370d7-123"><xref:System.Net.Http.HttpClient> [ `HttpClient` Fabrika altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir uygulama sağlayın.</span><span class="sxs-lookup"><span data-stu-id="370d7-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="370d7-124"><xref:System.Net.Http.HttpClient>ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="370d7-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="370d7-125"><xref:System.Net.Http.HttpClient>, tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="370d7-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="370d7-126">İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="370d7-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="370d7-127"><xref:System.Net.Http.HttpClient>Yönergesini kullanarak bir örnek ekleme [`@inject`](xref:mvc/views/razor#inject) :</span><span class="sxs-lookup"><span data-stu-id="370d7-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="370d7-128">Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler.</span><span class="sxs-lookup"><span data-stu-id="370d7-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="370d7-129">Örnekler `TodoItem` şunları depolayan bir sınıfa dayalıdır:</span><span class="sxs-lookup"><span data-stu-id="370d7-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="370d7-130">KIMLIK ( `Id` , `long` ): ÖĞENIN benzersiz kimliği.</span><span class="sxs-lookup"><span data-stu-id="370d7-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="370d7-131">Ad ( `Name` , `string` ): öğenin adı.</span><span class="sxs-lookup"><span data-stu-id="370d7-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="370d7-132">Durum ( `IsComplete` , `bool` ): Todo öğesinin tamamlanıp bitmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="370d7-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="370d7-133">JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:</span><span class="sxs-lookup"><span data-stu-id="370d7-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="370d7-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Bir HTTP GET isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="370d7-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="370d7-135">Aşağıdaki kodda,, `todoItems` bileşeni tarafından görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="370d7-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="370d7-136">`GetTodoItems`Yöntemi, bileşen işlemeyi tamamladığında tetiklenir ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ).</span><span class="sxs-lookup"><span data-stu-id="370d7-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="370d7-137">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="370d7-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="370d7-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: JSON kodlu içerik dahil bir HTTP POST isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="370d7-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="370d7-139">Aşağıdaki kodda, `newItemName` bileşenin bağlantılı bir öğesi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="370d7-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="370d7-140">`AddItem`Yöntemi bir öğesi seçilerek tetiklenir `<button>` .</span><span class="sxs-lookup"><span data-stu-id="370d7-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="370d7-141">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="370d7-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="370d7-142">Döndürecek çağrılar <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="370d7-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="370d7-143">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="370d7-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="370d7-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: JSON kodlu içerik dahil bir HTTP PUT isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="370d7-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="370d7-145">Aşağıdaki kodda, `editItem` `Name` ve değerleri `IsCompleted` bileşenin bağlantılı öğelerine göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="370d7-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="370d7-146">Öğe, `Id` Kullanıcı arabiriminin başka bir bölümünde seçildiğinde ayarlanır ve `EditItem` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="370d7-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="370d7-147">`SaveItem`Yöntemi, Kaydet öğesi seçilerek tetiklenir `<button>` .</span><span class="sxs-lookup"><span data-stu-id="370d7-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="370d7-148">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="370d7-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="370d7-149">Döndürecek çağrılar <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="370d7-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="370d7-150">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="370d7-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="370d7-151"><xref:System.Net.Http>HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="370d7-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="370d7-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>bir Web API 'sine HTTP DELETE isteği göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="370d7-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="370d7-153">Aşağıdaki kodda, Delete `<button>` öğesi `DeleteItem` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="370d7-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="370d7-154">Bağlantılı `<input>` öğe, `id` Silinecek öğenin bir listesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="370d7-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="370d7-155">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="370d7-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="370d7-156">Ihttpclientfactory ile adlandırılmış HttpClient</span><span class="sxs-lookup"><span data-stu-id="370d7-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="370d7-157"><xref:System.Net.Http.IHttpClientFactory>Hizmetler ve bir adlandırılmış yapılandırma <xref:System.Net.Http.HttpClient> desteklenir.</span><span class="sxs-lookup"><span data-stu-id="370d7-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="370d7-158">[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)Proje dosyasındaki NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="370d7-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package in the project file.</span></span>

<span data-ttu-id="370d7-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="370d7-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="370d7-160">`FetchData`bileşen ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="370d7-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="370d7-161">Yazılan HttpClient</span><span class="sxs-lookup"><span data-stu-id="370d7-161">Typed HttpClient</span></span>

<span data-ttu-id="370d7-162">Türü <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.HttpClient> bir veya daha fazla Web API uç noktasından veri döndürmek için, bir veya daha fazla uygulamanın örneklerinden, varsayılan veya adlandırılmış bir veya daha fazlasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="370d7-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="370d7-163">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="370d7-163">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="370d7-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="370d7-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="370d7-165">Bileşenler, <xref:System.Net.Http.HttpClient> Web API 'sini çağırmak için türü ekler.</span><span class="sxs-lookup"><span data-stu-id="370d7-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="370d7-166">`FetchData`bileşen ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="370d7-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="370d7-167">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="370d7-167">Handle errors</span></span>

<span data-ttu-id="370d7-168">Bir Web API 'SI ile etkileşirken hatalar oluştuğunda, geliştirici kodu tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="370d7-168">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="370d7-169">Örneğin, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ile sunucu API 'sinden BIR JSON yanıtı bekler `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="370d7-169">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="370d7-170">Yanıt JSON biçiminde değilse, içerik doğrulaması bir oluşturur <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="370d7-170">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="370d7-171">Aşağıdaki örnekte, hava durumu tahmin verileri isteği için URI uç noktası yanlış yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="370d7-171">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="370d7-172">URI 'nin olması gerekir `WeatherForecast` ancak çağrıda `WeatherForcast` (eksik "e") görünmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="370d7-172">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="370d7-173"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Çağrı JSON 'ın döndürülmesini bekler, ancak sunucu, ' a sahip sunucudaki işlenmeyen bir özel durum IÇIN HTML döndürür `Content-Type` `text/html` .</span><span class="sxs-lookup"><span data-stu-id="370d7-173">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="370d7-174">Yol bulunamadığı ve ara yazılım, istek için bir sayfa veya görünüm hizmeti veremediği için sunucuda işlenmeyen özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="370d7-174">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="370d7-175"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>İstemcisinde, <xref:System.NotSupportedException> yanıt içeriği JSON olmayan olarak doğrulandığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="370d7-175">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="370d7-176">Özel `catch` mantık hatayı günlüğe kaydetmek veya kullanıcıya kolay bir hata iletisi sunmak üzere bloğunda özel durum yakalanır:</span><span class="sxs-lookup"><span data-stu-id="370d7-176">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="370d7-177">Yukarıdaki örnek tanıtım amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="370d7-177">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="370d7-178">Bir Web API sunucu uygulaması, bir uç nokta mevcut olmadığında veya sunucu üzerinde işlenmeyen bir özel durum olsa bile JSON döndürecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="370d7-178">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="370d7-179">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="370d7-179">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="370d7-180">Çıkış noktaları arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="370d7-180">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="370d7-181">Tarayıcı güvenliği, bir Web sayfasının, Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="370d7-181">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="370d7-182">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="370d7-182">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="370d7-183">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="370d7-183">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="370d7-184">Tarayıcıdan farklı bir kaynağa sahip bir uç noktaya istek yapmak için *uç noktanın* , [çıkış noktaları arası kaynak PAYLAŞıMı 'nı (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="370d7-184">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="370d7-185">[ Blazor WebAssembly Örnek uygulama ( Blazor webassemblysample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , Web API bileşeni () çağrısı içinde CORS 'nin kullanımını gösterir `Pages/CallWebAPI.razor` .</span><span class="sxs-lookup"><span data-stu-id="370d7-185">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="370d7-186">Uygulamalarda güvenli isteklerle CORS hakkında daha fazla bilgi için Blazor bkz <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> ..</span><span class="sxs-lookup"><span data-stu-id="370d7-186">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="370d7-187">ASP.NET Core uygulamalarla CORS hakkında genel bilgi için bkz <xref:security/cors> ..</span><span class="sxs-lookup"><span data-stu-id="370d7-187">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="370d7-188">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="370d7-188">Additional resources</span></span>

* <span data-ttu-id="370d7-189"><xref:blazor/security/webassembly/additional-scenarios>: <xref:System.Net.Http.HttpClient> Güvenli Web API istekleri yapmak için kullanımı ile ilgili kapsamı içerir.</span><span class="sxs-lookup"><span data-stu-id="370d7-189"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="370d7-190">Kestrel HTTPS uç noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="370d7-190">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="370d7-191">W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="370d7-191">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
