---
title: ASP.NET Core Blazor webassembly 'ten BIR Web API 'si çağırma
author: guardrex
description: Çıkış noktaları arası kaynak paylaşımı (CORS) istekleri Blazor yapma dahil olmak üzere, JSON yardımcıları kullanarak webassembly uygulamasından BIR Web API 'si çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 7476e9dce3fa26948d2091235747f893d805d7be
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967278"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="7ebfd-103">ASP.NET Core bir Web API 'SI çağırmaBlazor</span><span class="sxs-lookup"><span data-stu-id="7ebfd-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="7ebfd-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından</span><span class="sxs-lookup"><span data-stu-id="7ebfd-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7ebfd-105">Webassembly uygulamaları önceden yapılandırılmış `HttpClient` bir hizmeti kullanarak Web API 'lerini çağırır. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="7ebfd-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="7ebfd-106">JSON yardımcıları veya ile birlikte <xref:System.Net.Http.HttpRequestMessage>JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçenekleri içerebilen oluşturma istekleri. Blazor</span><span class="sxs-lookup"><span data-stu-id="7ebfd-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="7ebfd-107">Webassembly Blazor Apps 'teki `HttpClient` hizmet, isteklerin kaynak sunucusuna geri getirilmesi üzerine odaklanır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="7ebfd-108">Bu konudaki kılavuz yalnızca Blazor webassembly uygulamalarına aittir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="7ebfd-109">Sunucu uygulamaları, genellikle kullanılarak <xref:System.Net.Http.IHttpClientFactory>oluşturulan <xref:System.Net.Http.HttpClient> örnekleri kullanarak Web API 'lerini çağırır. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="7ebfd-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="7ebfd-110">Bu konudaki kılavuz, Blazor sunucu uygulamalarıyla ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="7ebfd-111">Sunucu uygulamaları Blazor geliştirirken, içindeki <xref:fundamentals/http-requests>yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="7ebfd-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="7ebfd-113">*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="7ebfd-114">Web API çağrısı (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="7ebfd-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="7ebfd-115">HTTP Isteği Sınayıcısı (*Bileşenler/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="7ebfd-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="7ebfd-116">Paketler</span><span class="sxs-lookup"><span data-stu-id="7ebfd-116">Packages</span></span>

<span data-ttu-id="7ebfd-117">Proje dosyasındaki [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="7ebfd-118">HttpClient hizmetini ekleme</span><span class="sxs-lookup"><span data-stu-id="7ebfd-118">Add the HttpClient service</span></span>

<span data-ttu-id="7ebfd-119">İçinde `Program.Main`, zaten yoksa `HttpClient` bir hizmet ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="7ebfd-120">HttpClient ve JSON yardımcıları</span><span class="sxs-lookup"><span data-stu-id="7ebfd-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="7ebfd-121">Blazor Webassembly uygulamasında, [HttpClient](xref:fundamentals/http-requests) , istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="7ebfd-122">Blazor Sunucu uygulaması varsayılan olarak bir `HttpClient` hizmet içermez.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="7ebfd-123">`HttpClient` [HttpClient Factory altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir uygulama sağlayın.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="7ebfd-124">`HttpClient`ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="7ebfd-125">`HttpClient`, tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="7ebfd-126">İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="7ebfd-127">`@inject` Yönergesini kullanarak bir `HttpClient` örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="7ebfd-128">Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="7ebfd-129">Örnekler şunları depolayan bir `TodoItem` sınıfa dayalıdır:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="7ebfd-130">Öğenin kimliği`Id`( `long`, &ndash; ) benzersiz kimliği.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="7ebfd-131">Öğenin adı`Name`( `string`, &ndash; ).</span><span class="sxs-lookup"><span data-stu-id="7ebfd-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="7ebfd-132">Todo öğesi`IsComplete`tamamlandığında `bool`durum &ndash; (,) göstergesi.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="7ebfd-133">JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="7ebfd-134">`GetFromJsonAsync`&ndash; BIR http get isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7ebfd-135">Aşağıdaki kodda,, `todoItems` bileşeni tarafından görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="7ebfd-136">`GetTodoItems` Yöntemi, bileşen işlemeyi tamamladığında tetiklenir ([Onınitializedadsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="7ebfd-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="7ebfd-137">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="7ebfd-138">`PostAsJsonAsync`&ndash; JSON kodlu içerik dahil olmak üzere BIR http post isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7ebfd-139">Aşağıdaki kodda, `newItemName` bileşenin bağlantılı bir öğesi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="7ebfd-140">`AddItem` Yöntemi bir `<button>` öğesi seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="7ebfd-141">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="7ebfd-142">`PostAsJsonAsync` Döndürecek çağrılar <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="7ebfd-143">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="7ebfd-144">`PutAsJsonAsync`&ndash; JSON kodlu içerik dahil olmak üzere BIR http put isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="7ebfd-145">Aşağıdaki kodda, `editItem` `Name` ve `IsCompleted` değerleri bileşenin bağlantılı öğelerine göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="7ebfd-146">`Id` Öğe, Kullanıcı arabiriminin başka bir bölümünde seçildiğinde ayarlanır ve `EditItem` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="7ebfd-147">`SaveItem` Yöntemi, Kaydet `<button>` öğesi seçilerek tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="7ebfd-148">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-148">See the sample app for a complete example.</span></span>

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
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="7ebfd-149">`PutAsJsonAsync` Döndürecek çağrılar <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="7ebfd-150">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="7ebfd-151"><xref:System.Net.Http>HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="7ebfd-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) , BIR Web API 'SINE http delete isteği göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="7ebfd-153">Aşağıdaki kodda, Delete `<button>` öğesi `DeleteItem` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="7ebfd-154">Bağlantılı `<input>` öğe, silinecek öğenin `id` bir listesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="7ebfd-155">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-155">See the sample app for a complete example.</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="7ebfd-156">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="7ebfd-156">Handle errors</span></span>

<span data-ttu-id="7ebfd-157">Bir Web API 'SI ile etkileşirken hatalar oluştuğunda, geliştirici kodu tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-157">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="7ebfd-158">Örneğin, `GetFromJsonAsync` Ile `Content-Type` sunucu API 'sinden bir JSON yanıtı bekler. `application/json`</span><span class="sxs-lookup"><span data-stu-id="7ebfd-158">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="7ebfd-159">Yanıt JSON biçiminde değilse, içerik doğrulaması bir <xref:System.NotSupportedException>oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-159">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="7ebfd-160">Aşağıdaki örnekte, hava durumu tahmin verileri isteği için URI uç noktası yanlış yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-160">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="7ebfd-161">URI 'nin olması gerekir `WeatherForecast` ancak çağrıda `WeatherForcast` (eksik "e") görünmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-161">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="7ebfd-162">`GetFromJsonAsync` Çağrı JSON 'ın döndürülmesini bekler, ancak sunucu, ' a `Content-Type` sahip sunucudaki işlenmeyen BIR özel durum için HTML döndürür `text/html`.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-162">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="7ebfd-163">Yol bulunamadığı ve ara yazılım, istek için bir sayfa veya görünüm hizmeti veremediği için sunucuda işlenmeyen özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-163">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="7ebfd-164">`OnInitializedAsync` İstemcisinde, <xref:System.NotSupportedException> yanıt içeriği JSON olmayan olarak doğrulandığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-164">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="7ebfd-165">Özel mantık hatayı günlüğe kaydetmek veya `catch` kullanıcıya kolay bir hata iletisi sunmak üzere bloğunda özel durum yakalanır:</span><span class="sxs-lookup"><span data-stu-id="7ebfd-165">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="7ebfd-166">Yukarıdaki örnek tanıtım amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-166">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="7ebfd-167">Bir Web API sunucu uygulaması, bir uç nokta mevcut olmadığında veya sunucu üzerinde işlenmeyen bir durum oluştuğunda bile JSON döndürecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-167">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="7ebfd-168">Daha fazla bilgi için bkz. <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-168">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="7ebfd-169">Çıkış noktaları arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="7ebfd-169">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="7ebfd-170">Tarayıcı güvenliği, bir Web sayfasının, Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-170">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="7ebfd-171">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-171">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7ebfd-172">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-172">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7ebfd-173">Tarayıcıdan farklı bir kaynağa sahip bir uç noktaya istek yapmak için *uç noktanın* , [çıkış noktaları arası kaynak PAYLAŞıMı 'nı (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7ebfd-173">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="7ebfd-174">[Webassembly örnek uygulaması (BlazorWebAssemblySample), Web API bileşeninde CORS 'nin kullanımını gösterir (Pages/callwebapi. Razor). Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) *Pages/CallWebAPI.razor*</span><span class="sxs-lookup"><span data-stu-id="7ebfd-174">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="7ebfd-175">Diğer sitelerin uygulamanıza çıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapmasına izin vermek için bkz <xref:security/cors>..</span><span class="sxs-lookup"><span data-stu-id="7ebfd-175">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ebfd-176">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7ebfd-176">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="7ebfd-177">Kestrel HTTPS uç noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7ebfd-177">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="7ebfd-178">W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="7ebfd-178">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
