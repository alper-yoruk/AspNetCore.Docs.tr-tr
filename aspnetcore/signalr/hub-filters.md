---
title: ASP.NET Core hub filtrelerini kullanma SignalR
author: brecon
description: ASP.NET Core ' de hub filtrelerini nasıl kullanacağınızı öğrenin SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
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
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052727"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a>ASP.NET Core hub filtrelerini kullanma SignalR

Hub filtreleri:

* ASP.NET Core 5,0 veya üzeri sürümlerde kullanılabilir.
* Hub yöntemleri istemciler tarafından çağrılmadan önce ve sonra mantığın çalışmasına izin verin.

Bu makale, hub filtrelerini yazmak ve kullanmak için rehberlik sağlar.

## <a name="configure-hub-filters"></a>Hub filtrelerini yapılandırma

Hub filtreleri, genel olarak veya hub türüne göre uygulanabilir. Filtrelerin eklendiği sıra, filtrelerin çalıştırıldığı sıradır. Genel hub filtreleri yerel hub filtrelerinden önce çalışır.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

Hub filtresi, aşağıdaki yöntemlerle eklenebilir:

* Somut türe göre bir filtre ekleyin:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Bu, bağımlılık ekleme (dı) veya tür etkinleştirildikten sonra çözümlenir.

* Çalışma zamanı türüne göre bir filtre ekleyin:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Bu, dı veya türü etkinleştirilmiş olarak çözümlenir.

* Örneğe göre bir filtre ekleyin:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Bu örnek tek bir gibi kullanılacaktır. Tüm Hub yöntemi etkinleştirmeleri aynı örneği kullanır.

Hub filtreleri oluşturulur ve hub çağrısı başına silinir. Genel durumu filtrede veya durum olmadan depolamak istiyorsanız, daha iyi performans için bir tek olarak, hub filtresi türünü bir tekil olarak ekleyin. Alternatif olarak, isterseniz filtreyi bir örnek olarak ekleyin.

## <a name="create-hub-filters"></a>Hub filtreleri oluşturma

Öğesinden devralan bir sınıf bildirerek bir filtre oluşturun `IHubFilter` ve `InvokeMethodAsync` yöntemi ekleyin. Ayrıca, `OnConnectedAsync` `OnDisconnectedAsync` `OnConnectedAsync` ve `OnDisconnectedAsync` hub yöntemlerini sırasıyla kaydırmak için isteğe bağlı olarak uygulanabilecek bir de vardır.

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

Filtreler, ara yazılıma çok benzer. `next`Yöntemi sonraki filtreyi çağırır. Son filtre, hub yöntemini çağırır. Filtreler ayrıca, sonucu bekleyen ' dan elde edilen sonucu da saklayabilir `next` ve bir hub yöntemi döndürmeden önce bir mantığı çalıştırın `next` .

Bir filtrede bir hub yöntemi çağrısını atlamak için, çağırmak yerine türünde bir özel durum oluşturun `HubException` `next` . Bir sonuç bekliyorsanız istemci bir hata alır.

## <a name="use-hub-filters"></a>Hub filtrelerini kullan

Filtre mantığını yazarken, hub yöntemi adlarını denetlemek yerine hub yöntemlerindeki öznitelikleri kullanarak genel hale getirme seçeneğini deneyin.

Yasaklanmış ifadeler için bir hub yöntemi bağımsız değişkenini denetleyecek ve bulduğu tümceciklerin yerini alacak bir filtre düşünün `***` .
Bu örnek için bir `LanguageFilterAttribute` sınıfın tanımlandığını varsayalım. Sınıfı, `FilterArgument` özniteliği kullanılırken ayarlankullanılabilecek adlı bir özelliğe sahiptir.

1. Özniteliği, temizlenecek dize bağımsız değişkenine sahip hub yöntemine yerleştirin:

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. Özniteliği denetlemek için bir hub filtresi tanımlayın ve bir hub yöntemi bağımsız değişkeninde yasaklanmış tümcecikleri ile değiştirin `**_` :

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. Hub filtresini `Startup.ConfigureServices` yöntemine kaydedin. Her çağırma için yasaklanmış ifadeler listesinin yeniden başlatılmasını önlemek için Merkez filtresi bir tek olarak kaydedilir:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a>Hubıncationcontext nesnesi

`HubInvocationContext`Geçerli hub yöntemi çağrısı için bilgiler içerir.

| Özellik | Açıklama | Tür |
| ------ | ------ | ----------- |
| `Context ` | , `HubCallerContext` Bağlantıyla ilgili bilgiler içerir. | `HubCallerContext` |
| `Hub` | Bu hub yöntemi çağrısı için kullanılan hub örneği. | `Hub` |
| `HubMethodName` | Çağrılan hub yönteminin adı. | `string` |
| `HubMethodArguments` | Hub yöntemine geçirilen bağımsız değişkenlerin listesi. | `IReadOnlyList<string>` |
| `ServiceProvider` | Bu hub yöntemi çağrısı için kapsamlı hizmet sağlayıcısı. | `IServiceProvider` |
| `HubMethod` | Hub yöntemi bilgileri. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>HubLifetimeContext nesnesi

, `HubLifetimeContext` `OnConnectedAsync` Ve hub yöntemleriyle ilgili bilgiler içerir `OnDisconnectedAsync` .

| Özellik | Açıklama | Tür |
| ------ | ------ | ----------- |
| `Context ` | , `HubCallerContext` Bağlantıyla ilgili bilgiler içerir. | `HubCallerContext` |
| `Hub` | Bu hub yöntemi çağrısı için kullanılan hub örneği. | `Hub` |
| `ServiceProvider` | Bu hub yöntemi çağrısı için kapsamlı hizmet sağlayıcısı. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Yetkilendirme ve filtreler

[Hub yöntemlerinde yetkilendirme öznitelikleri](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) Merkez filtrelerinden önce çalışır.
