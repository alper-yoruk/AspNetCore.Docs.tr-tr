---
title: ASP.NET Core bir Web API 'SI çağırma Blazor WebAssembly
author: guardrex
description: Blazor WebAssemblyÇıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapma dahil olmak üzere JSON yardımcıları kullanarak bir Web API 'sini nasıl çağıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 18afa7faa254f89ef664e0188be357a85c790523
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252441"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a>ASP.NET Core bir Web API 'SI çağırma Blazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından

> [!NOTE]
> Bu konu için geçerlidir Blazor WebAssembly . [Blazor Server](xref:blazor/hosting-models#blazor-server) uygulamalar <xref:System.Net.Http.HttpClient> , genellikle kullanılarak oluşturulan örnekleri kullanarak Web API 'lerini çağırır <xref:System.Net.Http.IHttpClientFactory> . İçin geçerli olan rehberlik için Blazor Server bkz <xref:fundamentals/http-requests> ..

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) uygulamalar önceden yapılandırılmış bir hizmeti kullanarak Web API 'Lerini çağırır <xref:System.Net.Http.HttpClient> . JSON yardımcıları veya ile birlikte JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçenekleri içerebilen oluşturma istekleri Blazor <xref:System.Net.Http.HttpRequestMessage> . <xref:System.Net.Http.HttpClient>Uygulamalardaki hizmet, Blazor WebAssembly isteklerin kaynak sunucusuna geri getirilmesi üzerine odaklanır. Bu konudaki kılavuz yalnızca uygulamalar için geçerlidir Blazor WebAssembly .

[Örnek kodu görüntüle veya indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)): `BlazorWebAssemblySample` uygulamayı seçin.

Örnek uygulamada aşağıdaki bileşenlere bakın `BlazorWebAssemblySample` :

* Web API ( `Pages/CallWebAPI.razor` ) çağrısı
* HTTP Istek Sınayıcısı ( `Components/HTTPRequestTester.razor` )

## <a name="packages"></a>Paketler

[`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json)Proje dosyasındaki NuGet paketine başvurun.

## <a name="add-the-httpclient-service"></a>HttpClient hizmetini ekleme

İçinde `Program.Main` , <xref:System.Net.Http.HttpClient> zaten yoksa bir hizmet ekleyin:

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient ve JSON yardımcıları

Bir Blazor WebAssembly uygulamada, [`HttpClient`](xref:fundamentals/http-requests) istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.

Bir Blazor Server uygulama <xref:System.Net.Http.HttpClient> Varsayılan olarak bir hizmet içermez. <xref:System.Net.Http.HttpClient> [ `HttpClient` Fabrika altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir uygulama sağlayın.

<xref:System.Net.Http.HttpClient> ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır. <xref:System.Net.Http.HttpClient> , tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.

İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır. <xref:System.Net.Http.HttpClient>Yönergesini kullanarak bir örnek ekleme [`@inject`](xref:mvc/views/razor#inject) :

```razor
@using System.Net.Http
@inject HttpClient Http
```

Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler. Örnekler `TodoItem` şunları depolayan bir sınıfa dayalıdır:

* KIMLIK ( `Id` , `long` ): ÖĞENIN benzersiz kimliği.
* Ad ( `Name` , `string` ): öğenin adı.
* Durum ( `IsComplete` , `bool` ): Todo öğesinin tamamlanıp bitmediğini belirtir.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Bir HTTP GET isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda,, `todoItems` bileşeni tarafından görüntülenir. `GetTodoItems`Yöntemi, bileşen işlemeyi tamamladığında tetiklenir ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ). Örnek uygulamaya bkz. örnek uygulama.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: JSON kodlu içerik dahil bir HTTP POST isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda, `newItemName` bileşenin bağlantılı bir öğesi tarafından sağlanır. `AddItem`Yöntemi bir öğesi seçilerek tetiklenir `<button>` . Örnek uygulamaya bkz. örnek uygulama.

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
  
  Döndürecek çağrılar <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> . Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: JSON kodlu içerik dahil bir HTTP PUT isteği gönderir.

  Aşağıdaki kodda, `editItem` `Name` ve değerleri `IsCompleted` bileşenin bağlantılı öğelerine göre sağlanır. Öğe, `Id` Kullanıcı arabiriminin başka bir bölümünde seçildiğinde ayarlanır ve `EditItem` çağrılır. `SaveItem`Yöntemi, Kaydet öğesi seçilerek tetiklenir `<button>` . Örnek uygulamaya bkz. örnek uygulama.

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
  
  Döndürecek çağrılar <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> . Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> genişletme yöntemini kullanın:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> bir Web API 'sine HTTP DELETE isteği göndermek için kullanılır.

Aşağıdaki kodda, Delete `<button>` öğesi `DeleteItem` yöntemini çağırır. Bağlantılı `<input>` öğe, `id` Silinecek öğenin bir listesini sağlar. Örnek uygulamaya bkz. örnek uygulama.

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

## <a name="named-httpclient-with-ihttpclientfactory"></a>Ihttpclientfactory ile adlandırılmış HttpClient

<xref:System.Net.Http.IHttpClientFactory> Hizmetler ve bir adlandırılmış yapılandırma <xref:System.Net.Http.HttpClient> desteklenir.

[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)Proje dosyasındaki NuGet paketine başvurun.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData` bileşen ( `Pages/FetchData.razor` ):

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

## <a name="typed-httpclient"></a>Yazılan HttpClient

Türü <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.HttpClient> bir veya daha fazla Web API uç noktasından veri döndürmek için, bir veya daha fazla uygulamanın örneklerinden, varsayılan veya adlandırılmış bir veya daha fazlasını kullanır.

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
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

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Bileşenler, <xref:System.Net.Http.HttpClient> Web API 'sini çağırmak için türü ekler.

`FetchData` bileşen ( `Pages/FetchData.razor` ):

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` ve `HttpRequestMessage` Fetch API isteği seçenekleriyle

Bir uygulamada WebAssembly üzerinde çalışırken Blazor WebAssembly [`HttpClient`](xref:fundamentals/http-requests) ([API belgeleri](xref:System.Net.Http.HttpClient)) ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirmek için kullanılabilir. Örneğin, HTTP yöntemini ve istek üst bilgilerini belirtebilirsiniz. Aşağıdaki bileşen, `POST` sunucuda Yapılacaklar LISTESI API uç noktası için bir istek yapar ve yanıt gövdesini gösterir:

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

.NET WebAssembly 'ın uygulanması, <xref:System.Net.Http.HttpClient> [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır. Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır. 

HTTP getirme isteği seçenekleri <xref:System.Net.Http.HttpRequestMessage> , aşağıdaki tabloda gösterilen uzantı yöntemleriyle yapılandırılabilir.

| Genişletme yöntemi | Fetch isteği özelliği |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Daha genel genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .
 
HTTP yanıtı, Blazor WebAssembly yanıt içeriği üzerinde eşitleme okuma desteğini etkinleştirmek için genellikle bir uygulamada arabelleğe kaydedilir. Yanıt akışı desteğini etkinleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> istekteki genişletme yöntemini kullanın.

Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> genişletme yöntemini kullanın:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="handle-errors"></a>Hataları işleme

Bir Web API 'SI ile etkileşirken hatalar oluştuğunda, geliştirici kodu tarafından işlenebilir. Örneğin, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ile sunucu API 'sinden BIR JSON yanıtı bekler `Content-Type` `application/json` . Yanıt JSON biçiminde değilse, içerik doğrulaması bir oluşturur <xref:System.NotSupportedException> .

Aşağıdaki örnekte, hava durumu tahmin verileri isteği için URI uç noktası yanlış yazılmıştır. URI 'nin olması gerekir `WeatherForecast` ancak çağrıda `WeatherForcast` (eksik "e") görünmesi gerekir.

<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Çağrı JSON 'ın döndürülmesini bekler, ancak sunucu, ' a sahip sunucudaki işlenmeyen bir özel durum IÇIN HTML döndürür `Content-Type` `text/html` . Yol bulunamadığı ve ara yazılım, istek için bir sayfa veya görünüm hizmeti veremediği için sunucuda işlenmeyen özel durum oluşur.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>İstemcisinde, <xref:System.NotSupportedException> yanıt içeriği JSON olmayan olarak doğrulandığında oluşturulur. Özel `catch` mantık hatayı günlüğe kaydetmek veya kullanıcıya kolay bir hata iletisi sunmak üzere bloğunda özel durum yakalanır:

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
> Yukarıdaki örnek tanıtım amaçlıdır. Bir Web API sunucu uygulaması, bir uç nokta mevcut olmadığında veya sunucu üzerinde işlenmeyen bir özel durum olsa bile JSON döndürecek şekilde yapılandırılabilir.

Daha fazla bilgi için bkz. <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımı (CORS)

Tarayıcı güvenliği, bir Web sayfasının, Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller. Bu kısıtlamaya *aynı-Origin ilkesi* adı verilir. Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller. Tarayıcıdan farklı bir kaynağa sahip bir uç noktaya istek yapmak için *uç noktanın* , [çıkış noktaları arası kaynak PAYLAŞıMı 'nı (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.

[ Blazor WebAssembly Örnek uygulama ( Blazor webassemblysample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , Web API bileşeni () çağrısı içinde CORS 'nin kullanımını gösterir `Pages/CallWebAPI.razor` .

Uygulamalarda güvenli isteklerle CORS hakkında daha fazla bilgi için Blazor bkz <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> ..

ASP.NET Core uygulamalarla CORS hakkında genel bilgi için bkz <xref:security/cors> ..

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/security/webassembly/additional-scenarios>: <xref:System.Net.Http.HttpClient> Güvenli Web API istekleri yapmak için kullanımı ile ilgili kapsamı içerir.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
::: moniker range=">= aspnetcore-5.0"
* [Kestrel HTTPS uç noktası yapılandırması](xref:fundamentals/servers/kestrel/endpoints)
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* [Kestrel HTTPS uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration)
::: moniker-end
* [W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/)
