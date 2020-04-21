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
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>ASP.NET Core'dan web API'sını arayınBlazor

Luke [Latham](https://github.com/guardrex)tarafından , [Daniel Roth](https://github.com/danroth27), ve Juan De la [Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

WebAssembly uygulamaları önceden yapılandırılmış bir `HttpClient` hizmeti kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) JSON yardımcılarını kullanarak Blazor veya JavaScript <xref:System.Net.Http.HttpRequestMessage>Getir [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçeneklerini içerebilen istekler oluşturun. WebAssembly `HttpClient` Blazor uygulamalarındaki hizmet, istekleri menşe sunucusuna geri yapmaya odaklanmıştır. Bu konudaki kılavuz yalnızca Blazor WebAssembly uygulamalarıyla ilgilidir.

Sunucu uygulamaları genellikle kullanılarak <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory>oluşturulan örnekleri kullanarak web API'lerini arar. [ Blazor ](xref:blazor/hosting-models#blazor-server) Bu konudaki kılavuz Sunucu uygulamalarıyla Blazor ilgili değildir. Sunucu Blazor uygulamaları geliştirirken, 'deki <xref:fundamentals/http-requests>kılavuzu izleyin.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.

*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:

* Arama Web API (*Sayfalar / CallWebAPI.razor*)
* HTTP İstek Tester (*Bileşenleri / HTTPRequestTester.razor*)

## <a name="packages"></a>Paketler

Proje dosyasındaki [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.

## <a name="add-the-httpclient-service"></a>HttpClient hizmetini ekleme

In, `Program.Main`zaten `HttpClient` yoksa bir hizmet ekleyin:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Httpİsteve JSON yardımcıları

Bir Blazor WebAssembly [uygulamasında, HttpClient](xref:fundamentals/http-requests) istekleri kaynak sunucuya geri yapmak için önceden yapılandırılmış bir hizmet olarak kullanılabilir.

Sunucu Blazor uygulaması varsayılan olarak `HttpClient` bir hizmet içermez. `HttpClient` [HttpClient fabrika altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir değer sağlayın.

`HttpClient`ve JSON yardımcıları da üçüncü taraf web API uç noktaları aramak için kullanılır. `HttpClient`tarayıcı [Getir API](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesinin uygulanması da dahil olmak üzere kendi sınırlamalarına tabidir.

İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır. Yönergeyi kullanarak bir `HttpClient` örnek enjekte edin: `@inject`

```razor
@using System.Net.Http
@inject HttpClient Http
```

Aşağıdaki örneklerde, bir Todo web API işlemleri (CRUD) işlemleri oluşturur, okur, günceller ve siler. Örnekler, aşağıdakileri `TodoItem` depolayan bir sınıfa dayanır:

* Kimlik`Id`( `long` &ndash; , ) Öğenin benzersiz kimliği.
* Ad`Name`( `string` &ndash; , ) Öğenin adı.
* Durum`IsComplete`( `bool` &ndash; , ) Todo öğesinin bitip bitmeyişini belirtin.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON yardımcı yöntemleri bir URI'ye (aşağıdaki örneklerdeki bir web API) istek gönderir ve yanıtı işlemeye

* `GetFromJsonAsync`&ndash; Bir HTTP GET isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda, `_todoItems` bileşen tarafından görüntülenir. Yöntem, `GetTodoItems` bileşen işleme tamamlandığında tetiklenir ([OnInitializedAsync).](xref:blazor/lifecycle#component-initialization-methods) Tam bir örnek için örnek uygulamaya bakın.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; JSON kodlanmış içeriği içeren bir HTTP POST isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda, `_newItemName` bileşenin bağlı bir öğesi tarafından sağlanır. Yöntem `AddItem` bir `<button>` öğe seçilerek tetiklenir. Tam bir örnek için örnek uygulamaya bakın.

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
  
  `PostAsJsonAsync` Bir <xref:System.Net.Http.HttpResponseMessage>. Yanıt iletisinden JSON içeriğini deserialize etmek `ReadFromJsonAsync<T>` için uzantı yöntemini kullanın:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; JSON kodlanmış içerik de dahil olmak üzere bir HTTP PUT isteği gönderir.

  Aşağıdaki kodda, `_editItem` bileşenin `IsCompleted` bağlı elemanları tarafından `Name` sağlanan değerler. Öğe UI'nin `Id` başka bir bölümünde seçildiğinde ve `EditItem` çağrıldığında maddenin ki ayarlanır. Yöntem `SaveItem` Kaydet `<button>` öğesi seçilerek tetiklenir. Tam bir örnek için örnek uygulamaya bakın.

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
  
  `PutAsJsonAsync` Bir <xref:System.Net.Http.HttpResponseMessage>. Yanıt iletisinden JSON içeriğini deserialize etmek `ReadFromJsonAsync<T>` için uzantı yöntemini kullanın:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>HTTP isteklerini göndermek ve HTTP yanıtları almak için ek uzatma yöntemleri içerir. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) bir WEB API'ya HTTP DELETE isteği göndermek için kullanılır.

Aşağıdaki kodda Sil `<button>` öğesi `DeleteItem` yöntemi çağırır. Bağlı `<input>` öğe, `id` silmek için öğenin sağlar. Tam bir örnek için örnek uygulamaya bakın.

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

## <a name="cross-origin-resource-sharing-cors"></a>Orijinler arası kaynak paylaşımı (CORS)

Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller. Bu kısıtlamaya *aynı kaynak ilkesi*denir. Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller. Tarayıcıdan farklı bir başlangıç noktası olan bir bitiş noktasına istekte bulunmak *için, bitiş noktasının* [kökenler arası kaynak paylaşımını (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.

WebAssembly örnek uygulaması (BlazorWebAssemblySample) Çağrı Web API bileşeninde CORS kullanımını gösterir (*Sayfalar / CallWebAPI.razor*). [ Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)

Diğer sitelerin uygulamanızda kökenler arası kaynak paylaşımı (CORS) <xref:security/cors>isteklerini yerine getirmelerine izin vermek için bkz.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>ApI Getir istek seçenekleriyle HttpClient ve HttpRequestMessage

WebAssembly uygulamasında çalışırken [HttpClient'ı](xref:fundamentals/http-requests) kullanın ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirin. Blazor Örneğin, uri, HTTP yöntemi ve istenen istek üstbilgilerini belirtebilirsiniz.

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

API Getir seçenekleri hakkında daha fazla bilgi için [BKZ: MDN web dokümanları: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

CORS isteklerine kimlik bilgileri (yetkilendirme çerezleri/üstbilgiler) gönderirken, üstbilginin `Authorization` CORS ilkesi tarafından izin verilmesi gerekir.

Aşağıdaki ilke aşağıdakiler için yapılandırmayı içerir:

* İstek kökenleri`http://localhost:5000` `https://localhost:5001`( , ).
* Herhangi bir yöntem (fiil).
* `Content-Type`ve `Authorization` üstbilgi. Özel bir üstbilgi (örneğin, `x-custom-header`), ararken üstbilgi listelemek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.
* İstemci tarafı JavaScript koduna göre ayarlanan kimlik bilgileri (özellik`credentials` olarak `include`ayarlanır).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Daha fazla bilgi <xref:security/cors> için, bkz ve örnek uygulamanın HTTP İstek Tester bileşeni *(Components/HTTPRequestTester.razor).*

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kerkenez HTTPS uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C'de Çapraz Köken Kaynak Paylaşımı (CORS)](https://www.w3.org/TR/cors/)
