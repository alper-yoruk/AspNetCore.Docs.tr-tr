---
title: ASP.NET Core webassembly 'ten bir Web API 'SI çağırma Blazor
author: guardrex
description: BlazorÇıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapma dahil olmak üzere, JSON yardımcıları kullanarak webassembly uygulamasından bir Web API 'si çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 7ed2d51c0d41a50a2e139d739a0a06cd9f392a83
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153498"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>ASP.NET Core bir Web API 'SI çağırmaBlazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[ Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) uygulamaları önceden yapılandırılmış bir hizmeti kullanarak Web API 'lerini çağırır `HttpClient` . JSON yardımcıları veya ile birlikte JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçenekleri içerebilen oluşturma istekleri Blazor <xref:System.Net.Http.HttpRequestMessage> . `HttpClient` Blazor Webassembly Apps 'teki hizmet, isteklerin kaynak sunucusuna geri getirilmesi üzerine odaklanır. Bu konudaki kılavuz yalnızca Blazor webassembly uygulamalarına aittir.

[ Blazor Sunucu](xref:blazor/hosting-models#blazor-server) uygulamaları <xref:System.Net.Http.HttpClient> , genellikle kullanılarak oluşturulan örnekleri kullanarak Web API 'lerini çağırır <xref:System.Net.Http.IHttpClientFactory> . Bu konudaki kılavuz, sunucu uygulamalarıyla ilgili değildir Blazor . BlazorSunucu uygulamaları geliştirirken, içindeki yönergeleri izleyin <xref:fundamentals/http-requests> .

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.

*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:

* Web API çağrısı (*Pages/CallWebAPI. Razor*)
* HTTP Isteği Sınayıcısı (*Bileşenler/HTTPRequestTester. Razor*)

## <a name="packages"></a>Paketler

Proje dosyasındaki [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.

## <a name="add-the-httpclient-service"></a>HttpClient hizmetini ekleme

İçinde `Program.Main` , `HttpClient` zaten yoksa bir hizmet ekleyin:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient ve JSON yardımcıları

BlazorWebassembly uygulamasında, [HttpClient](xref:fundamentals/http-requests) , istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.

BlazorSunucu uygulaması `HttpClient` Varsayılan olarak bir hizmet içermez. `HttpClient` [HttpClient Factory altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir uygulama sağlayın.

`HttpClient`ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır. `HttpClient`, tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.

İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır. `HttpClient`Yönergesini kullanarak bir örnek ekleme `@inject` :

```razor
@using System.Net.Http
@inject HttpClient Http
```

Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler. Örnekler `TodoItem` şunları depolayan bir sınıfa dayalıdır:

* Öğenin kimliği ( `Id` , `long` ) &ndash; benzersiz kimliği.
* Öğenin adı ( `Name` , `string` ) &ndash; .
* `IsComplete` `bool` Todo öğesi tamamlandığında durum (,) &ndash; göstergesi.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:

* `GetFromJsonAsync`&ndash;BIR http get isteği gönderir ve bir nesne oluşturmak IÇIN JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda,, `todoItems` bileşeni tarafından görüntülenir. `GetTodoItems`Yöntemi, bileşen işlemeyi tamamladığında tetiklenir ([Onınitializedadsync](xref:blazor/lifecycle#component-initialization-methods)). Örnek uygulamaya bkz. örnek uygulama.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash;JSON kodlu içerik dahil olmak üzere BIR http post isteği gönderir ve bir nesne oluşturmak IÇIN JSON yanıt gövdesini ayrıştırır.

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
  
  Döndürecek çağrılar `PostAsJsonAsync` <xref:System.Net.Http.HttpResponseMessage> . Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash;JSON kodlu içerik dahil olmak üzere BIR http put isteği gönderir.

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
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  Döndürecek çağrılar `PutAsJsonAsync` <xref:System.Net.Http.HttpResponseMessage> . Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) , BIR Web API 'SINE http delete isteği göndermek için kullanılır.

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

<xref:System.Net.Http.IHttpClientFactory>Hizmetler ve bir adlandırılmış yapılandırma <xref:System.Net.Http.HttpClient> desteklenir.

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData`bileşen (*Pages/FetchData. Razor*):

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

*WeatherForecastClient.cs*:

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

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Bileşenler, `HttpClient` Web API 'sini çağırmak için türü ekler.

`FetchData`bileşen (*Pages/FetchData. Razor*):

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

## <a name="handle-errors"></a>Hataları işleme

Bir Web API 'SI ile etkileşirken hatalar oluştuğunda, geliştirici kodu tarafından işlenebilir. Örneğin, `GetFromJsonAsync` ile sunucu API 'sinden BIR JSON yanıtı bekler `Content-Type` `application/json` . Yanıt JSON biçiminde değilse, içerik doğrulaması bir oluşturur <xref:System.NotSupportedException> .

Aşağıdaki örnekte, hava durumu tahmin verileri isteği için URI uç noktası yanlış yazılmıştır. URI 'nin olması gerekir `WeatherForecast` ancak çağrıda `WeatherForcast` (eksik "e") görünmesi gerekir.

`GetFromJsonAsync`Çağrı JSON 'ın döndürülmesini bekler, ancak sunucu, ' a sahip sunucudaki işlenmeyen bir özel durum IÇIN HTML döndürür `Content-Type` `text/html` . Yol bulunamadığı ve ara yazılım, istek için bir sayfa veya görünüm hizmeti veremediği için sunucuda işlenmeyen özel durum oluşur.

`OnInitializedAsync`İstemcisinde, <xref:System.NotSupportedException> yanıt içeriği JSON olmayan olarak doğrulandığında oluşturulur. Özel `catch` mantık hatayı günlüğe kaydetmek veya kullanıcıya kolay bir hata iletisi sunmak üzere bloğunda özel durum yakalanır:

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
> Yukarıdaki örnek tanıtım amaçlıdır. Bir Web API sunucu uygulaması, bir uç nokta mevcut olmadığında veya sunucu üzerinde işlenmeyen bir durum oluştuğunda bile JSON döndürecek şekilde yapılandırılabilir.

Daha fazla bilgi için bkz. <xref:blazor/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımı (CORS)

Tarayıcı güvenliği, bir Web sayfasının, Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller. Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir. Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller. Tarayıcıdan farklı bir kaynağa sahip bir uç noktaya istek yapmak için *uç noktanın* , [çıkış noktaları arası kaynak PAYLAŞıMı 'nı (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.

[ Blazor Webassembly örnek uygulaması (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , Web API bileşeninde CORS 'nin kullanımını gösterir (*Pages/callwebapi. Razor*).

Diğer sitelerin uygulamanıza çıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapmasına izin vermek için bkz <xref:security/cors> ..

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/webassembly/additional-scenarios>&ndash; `HttpClient` GÜVENLI Web API istekleri yapmak için kullanımı ile ilgili kapsamı içerir.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/)
