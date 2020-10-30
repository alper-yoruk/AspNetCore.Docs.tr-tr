---
title: 'ASP.NET Core hub filtrelerini kullanma :::no-loc(SignalR):::'
author: brecon
description: "ASP.NET Core ' de hub filtrelerini nasıl kullanacağınızı öğrenin :::no-loc(SignalR)::: ."
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052727"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="a9a00-103">ASP.NET Core hub filtrelerini kullanma :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="a9a00-103">Use hub filters in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="a9a00-104">Hub filtreleri:</span><span class="sxs-lookup"><span data-stu-id="a9a00-104">Hub filters:</span></span>

* <span data-ttu-id="a9a00-105">ASP.NET Core 5,0 veya üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="a9a00-106">Hub yöntemleri istemciler tarafından çağrılmadan önce ve sonra mantığın çalışmasına izin verin.</span><span class="sxs-lookup"><span data-stu-id="a9a00-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="a9a00-107">Bu makale, hub filtrelerini yazmak ve kullanmak için rehberlik sağlar.</span><span class="sxs-lookup"><span data-stu-id="a9a00-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="a9a00-108">Hub filtrelerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a9a00-108">Configure hub filters</span></span>

<span data-ttu-id="a9a00-109">Hub filtreleri, genel olarak veya hub türüne göre uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="a9a00-110">Filtrelerin eklendiği sıra, filtrelerin çalıştırıldığı sıradır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="a9a00-111">Genel hub filtreleri yerel hub filtrelerinden önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(options =>
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

<span data-ttu-id="a9a00-112">Hub filtresi, aşağıdaki yöntemlerle eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="a9a00-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="a9a00-113">Somut türe göre bir filtre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a9a00-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="a9a00-114">Bu, bağımlılık ekleme (dı) veya tür etkinleştirildikten sonra çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="a9a00-115">Çalışma zamanı türüne göre bir filtre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a9a00-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="a9a00-116">Bu, dı veya türü etkinleştirilmiş olarak çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="a9a00-117">Örneğe göre bir filtre ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a9a00-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="a9a00-118">Bu örnek tek bir gibi kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="a9a00-119">Tüm Hub yöntemi etkinleştirmeleri aynı örneği kullanır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="a9a00-120">Hub filtreleri oluşturulur ve hub çağrısı başına silinir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="a9a00-121">Genel durumu filtrede veya durum olmadan depolamak istiyorsanız, daha iyi performans için bir tek olarak, hub filtresi türünü bir tekil olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a9a00-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="a9a00-122">Alternatif olarak, isterseniz filtreyi bir örnek olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a9a00-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="a9a00-123">Hub filtreleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="a9a00-123">Create hub filters</span></span>

<span data-ttu-id="a9a00-124">Öğesinden devralan bir sınıf bildirerek bir filtre oluşturun `IHubFilter` ve `InvokeMethodAsync` yöntemi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a9a00-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="a9a00-125">Ayrıca, `OnConnectedAsync` `OnDisconnectedAsync` `OnConnectedAsync` ve `OnDisconnectedAsync` hub yöntemlerini sırasıyla kaydırmak için isteğe bağlı olarak uygulanabilecek bir de vardır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

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

<span data-ttu-id="a9a00-126">Filtreler, ara yazılıma çok benzer.</span><span class="sxs-lookup"><span data-stu-id="a9a00-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="a9a00-127">`next`Yöntemi sonraki filtreyi çağırır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="a9a00-128">Son filtre, hub yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="a9a00-129">Filtreler ayrıca, sonucu bekleyen ' dan elde edilen sonucu da saklayabilir `next` ve bir hub yöntemi döndürmeden önce bir mantığı çalıştırın `next` .</span><span class="sxs-lookup"><span data-stu-id="a9a00-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="a9a00-130">Bir filtrede bir hub yöntemi çağrısını atlamak için, çağırmak yerine türünde bir özel durum oluşturun `HubException` `next` .</span><span class="sxs-lookup"><span data-stu-id="a9a00-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="a9a00-131">Bir sonuç bekliyorsanız istemci bir hata alır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="a9a00-132">Hub filtrelerini kullan</span><span class="sxs-lookup"><span data-stu-id="a9a00-132">Use hub filters</span></span>

<span data-ttu-id="a9a00-133">Filtre mantığını yazarken, hub yöntemi adlarını denetlemek yerine hub yöntemlerindeki öznitelikleri kullanarak genel hale getirme seçeneğini deneyin.</span><span class="sxs-lookup"><span data-stu-id="a9a00-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="a9a00-134">Yasaklanmış ifadeler için bir hub yöntemi bağımsız değişkenini denetleyecek ve bulduğu tümceciklerin yerini alacak bir filtre düşünün `***` .</span><span class="sxs-lookup"><span data-stu-id="a9a00-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="a9a00-135">Bu örnek için bir `LanguageFilterAttribute` sınıfın tanımlandığını varsayalım.</span><span class="sxs-lookup"><span data-stu-id="a9a00-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="a9a00-136">Sınıfı, `FilterArgument` özniteliği kullanılırken ayarlankullanılabilecek adlı bir özelliğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="a9a00-137">Özniteliği, temizlenecek dize bağımsız değişkenine sahip hub yöntemine yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="a9a00-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

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

1. <span data-ttu-id="a9a00-138">Özniteliği denetlemek için bir hub filtresi tanımlayın ve bir hub yöntemi bağımsız değişkeninde yasaklanmış tümcecikleri ile değiştirin `**_` :</span><span class="sxs-lookup"><span data-stu-id="a9a00-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `**_`:</span></span>

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

1. <span data-ttu-id="a9a00-139">Hub filtresini `Startup.ConfigureServices` yöntemine kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a9a00-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a9a00-140">Her çağırma için yasaklanmış ifadeler listesinin yeniden başlatılmasını önlemek için Merkez filtresi bir tek olarak kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a9a00-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(SignalR):::(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="a9a00-141">Hubıncationcontext nesnesi</span><span class="sxs-lookup"><span data-stu-id="a9a00-141">The HubInvocationContext object</span></span>

<span data-ttu-id="a9a00-142">`HubInvocationContext`Geçerli hub yöntemi çağrısı için bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="a9a00-143">Özellik</span><span class="sxs-lookup"><span data-stu-id="a9a00-143">Property</span></span> | <span data-ttu-id="a9a00-144">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a9a00-144">Description</span></span> | <span data-ttu-id="a9a00-145">Tür</span><span class="sxs-lookup"><span data-stu-id="a9a00-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="a9a00-146">, `HubCallerContext` Bağlantıyla ilgili bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="a9a00-147">Bu hub yöntemi çağrısı için kullanılan hub örneği.</span><span class="sxs-lookup"><span data-stu-id="a9a00-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="a9a00-148">Çağrılan hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="a9a00-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="a9a00-149">Hub yöntemine geçirilen bağımsız değişkenlerin listesi.</span><span class="sxs-lookup"><span data-stu-id="a9a00-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="a9a00-150">Bu hub yöntemi çağrısı için kapsamlı hizmet sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="a9a00-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="a9a00-151">Hub yöntemi bilgileri.</span><span class="sxs-lookup"><span data-stu-id="a9a00-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="a9a00-152">HubLifetimeContext nesnesi</span><span class="sxs-lookup"><span data-stu-id="a9a00-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="a9a00-153">, `HubLifetimeContext` `OnConnectedAsync` Ve hub yöntemleriyle ilgili bilgiler içerir `OnDisconnectedAsync` .</span><span class="sxs-lookup"><span data-stu-id="a9a00-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="a9a00-154">Özellik</span><span class="sxs-lookup"><span data-stu-id="a9a00-154">Property</span></span> | <span data-ttu-id="a9a00-155">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a9a00-155">Description</span></span> | <span data-ttu-id="a9a00-156">Tür</span><span class="sxs-lookup"><span data-stu-id="a9a00-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="a9a00-157">, `HubCallerContext` Bağlantıyla ilgili bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="a9a00-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="a9a00-158">Bu hub yöntemi çağrısı için kullanılan hub örneği.</span><span class="sxs-lookup"><span data-stu-id="a9a00-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="a9a00-159">Bu hub yöntemi çağrısı için kapsamlı hizmet sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="a9a00-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="a9a00-160">Yetkilendirme ve filtreler</span><span class="sxs-lookup"><span data-stu-id="a9a00-160">Authorization and filters</span></span>

<span data-ttu-id="a9a00-161">[Hub yöntemlerinde yetkilendirme öznitelikleri](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) Merkez filtrelerinden önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="a9a00-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
