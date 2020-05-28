---
<span data-ttu-id="1de9c-101">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-103">'Blazor'</span></span>
- <span data-ttu-id="1de9c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-104">'Identity'</span></span>
- <span data-ttu-id="1de9c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-106">'Razor'</span></span>
- <span data-ttu-id="1de9c-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="1de9c-108">ASP.NET Core Blazor olay işleme</span><span class="sxs-lookup"><span data-stu-id="1de9c-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="1de9c-109">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="1de9c-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="1de9c-110">bileşenler olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="1de9c-110"> components provide event handling features.</span></span> <span data-ttu-id="1de9c-111">[`@on{EVENT}`](xref:mvc/views/razor#onevent)Temsilci türü belirtilmiş bir değer ile adlı BIR HTML öğesi özniteliği için (örneğin, `@onclick` ), bir Razor bileşen öznitelik değerini bir olay işleyicisi olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="1de9c-112">Aşağıdaki kod, `UpdateHeading` Kullanıcı arabiriminde düğme seçildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="1de9c-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="1de9c-113">Aşağıdaki kod, `CheckChanged` Kullanıcı arabiriminde onay kutusu değiştirildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="1de9c-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="1de9c-114">Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve döndürebilir <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="1de9c-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="1de9c-115">[Statehaschanged](xref:blazor/lifecycle#state-changes)el ile çağırmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1de9c-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="1de9c-116">Özel durumlar oluştuğunda günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="1de9c-117">Aşağıdaki örnekte, `UpdateHeading` Düğme seçildiğinde zaman uyumsuz olarak çağrılır:</span><span class="sxs-lookup"><span data-stu-id="1de9c-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="1de9c-118">Olay bağımsız değişken türleri</span><span class="sxs-lookup"><span data-stu-id="1de9c-118">Event argument types</span></span>

<span data-ttu-id="1de9c-119">Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="1de9c-120">Yöntem çağrısında bir olay türü belirtmek yalnızca, olay türü yönteminde kullanılıyorsa gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="1de9c-121">Desteklenir <xref:System.EventArgs> , aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="1de9c-122">Olay</span><span class="sxs-lookup"><span data-stu-id="1de9c-122">Event</span></span>            | <span data-ttu-id="1de9c-123">Sınıf</span><span class="sxs-lookup"><span data-stu-id="1de9c-123">Class</span></span>                | <span data-ttu-id="1de9c-124">DOM olayları ve notları</span><span class="sxs-lookup"><span data-stu-id="1de9c-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="1de9c-125">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-126">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-127">'Blazor'</span></span>
- <span data-ttu-id="1de9c-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-128">'Identity'</span></span>
- <span data-ttu-id="1de9c-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-130">'Razor'</span></span>
- <span data-ttu-id="1de9c-131">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-132">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-133">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-134">'Blazor'</span></span>
- <span data-ttu-id="1de9c-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-135">'Identity'</span></span>
- <span data-ttu-id="1de9c-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-137">'Razor'</span></span>
- <span data-ttu-id="1de9c-138">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-139">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-140">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-141">'Blazor'</span></span>
- <span data-ttu-id="1de9c-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-142">'Identity'</span></span>
- <span data-ttu-id="1de9c-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-144">'Razor'</span></span>
- <span data-ttu-id="1de9c-145">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-146">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-147">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-148">'Blazor'</span></span>
- <span data-ttu-id="1de9c-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-149">'Identity'</span></span>
- <span data-ttu-id="1de9c-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-151">'Razor'</span></span>
- <span data-ttu-id="1de9c-152">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-153">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-154">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-155">'Blazor'</span></span>
- <span data-ttu-id="1de9c-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-156">'Identity'</span></span>
- <span data-ttu-id="1de9c-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-158">'Razor'</span></span>
- <span data-ttu-id="1de9c-159">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-160">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-161">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-162">'Blazor'</span></span>
- <span data-ttu-id="1de9c-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-163">'Identity'</span></span>
- <span data-ttu-id="1de9c-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-165">'Razor'</span></span>
- <span data-ttu-id="1de9c-166">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-166">'SignalR' uid:</span></span> 

<span data-ttu-id="1de9c-167">-------- | ---title: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-168">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-169">'Blazor'</span></span>
- <span data-ttu-id="1de9c-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-170">'Identity'</span></span>
- <span data-ttu-id="1de9c-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-172">'Razor'</span></span>
- <span data-ttu-id="1de9c-173">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-174">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-175">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-176">'Blazor'</span></span>
- <span data-ttu-id="1de9c-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-177">'Identity'</span></span>
- <span data-ttu-id="1de9c-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-179">'Razor'</span></span>
- <span data-ttu-id="1de9c-180">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-181">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-182">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-183">'Blazor'</span></span>
- <span data-ttu-id="1de9c-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-184">'Identity'</span></span>
- <span data-ttu-id="1de9c-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-186">'Razor'</span></span>
- <span data-ttu-id="1de9c-187">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-188">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-189">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-190">'Blazor'</span></span>
- <span data-ttu-id="1de9c-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-191">'Identity'</span></span>
- <span data-ttu-id="1de9c-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-193">'Razor'</span></span>
- <span data-ttu-id="1de9c-194">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-195">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-196">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-197">'Blazor'</span></span>
- <span data-ttu-id="1de9c-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-198">'Identity'</span></span>
- <span data-ttu-id="1de9c-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-200">'Razor'</span></span>
- <span data-ttu-id="1de9c-201">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-202">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-203">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-204">'Blazor'</span></span>
- <span data-ttu-id="1de9c-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-205">'Identity'</span></span>
- <span data-ttu-id="1de9c-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-207">'Razor'</span></span>
- <span data-ttu-id="1de9c-208">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-209">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-210">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-211">'Blazor'</span></span>
- <span data-ttu-id="1de9c-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-212">'Identity'</span></span>
- <span data-ttu-id="1de9c-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-214">'Razor'</span></span>
- <span data-ttu-id="1de9c-215">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-216">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-217">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-218">'Blazor'</span></span>
- <span data-ttu-id="1de9c-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-219">'Identity'</span></span>
- <span data-ttu-id="1de9c-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-221">'Razor'</span></span>
- <span data-ttu-id="1de9c-222">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-222">'SignalR' uid:</span></span> 

<span data-ttu-id="1de9c-223">---------- | ---title: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-224">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-225">'Blazor'</span></span>
- <span data-ttu-id="1de9c-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-226">'Identity'</span></span>
- <span data-ttu-id="1de9c-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-228">'Razor'</span></span>
- <span data-ttu-id="1de9c-229">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-230">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-231">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-232">'Blazor'</span></span>
- <span data-ttu-id="1de9c-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-233">'Identity'</span></span>
- <span data-ttu-id="1de9c-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-235">'Razor'</span></span>
- <span data-ttu-id="1de9c-236">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-237">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-238">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-239">'Blazor'</span></span>
- <span data-ttu-id="1de9c-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-240">'Identity'</span></span>
- <span data-ttu-id="1de9c-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-242">'Razor'</span></span>
- <span data-ttu-id="1de9c-243">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-244">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-245">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-246">'Blazor'</span></span>
- <span data-ttu-id="1de9c-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-247">'Identity'</span></span>
- <span data-ttu-id="1de9c-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-249">'Razor'</span></span>
- <span data-ttu-id="1de9c-250">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-251">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-252">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-253">'Blazor'</span></span>
- <span data-ttu-id="1de9c-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-254">'Identity'</span></span>
- <span data-ttu-id="1de9c-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-256">'Razor'</span></span>
- <span data-ttu-id="1de9c-257">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-258">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-259">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-260">'Blazor'</span></span>
- <span data-ttu-id="1de9c-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-261">'Identity'</span></span>
- <span data-ttu-id="1de9c-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-263">'Razor'</span></span>
- <span data-ttu-id="1de9c-264">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-265">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-266">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-267">'Blazor'</span></span>
- <span data-ttu-id="1de9c-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-268">'Identity'</span></span>
- <span data-ttu-id="1de9c-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-270">'Razor'</span></span>
- <span data-ttu-id="1de9c-271">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1de9c-272">Başlık: ' ASP.NET Core Blazor olay işleme ' Yazar: Açıklama: ' Blazor olay bağımsız değişkeni türleri, olay geri çağırmaları ve varsayılan tarayıcı olaylarını yönetmek dahil olmak üzere olay işleme özellikleri hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="1de9c-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1de9c-273">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1de9c-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1de9c-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-274">'Blazor'</span></span>
- <span data-ttu-id="1de9c-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1de9c-275">'Identity'</span></span>
- <span data-ttu-id="1de9c-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1de9c-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="1de9c-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1de9c-277">'Razor'</span></span>
- <span data-ttu-id="1de9c-278">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1de9c-278">'SignalR' uid:</span></span> 

<span data-ttu-id="1de9c-279">---------- | | Pano | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Sürükleyin | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`</span><span class="sxs-lookup"><span data-stu-id="1de9c-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="1de9c-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>ve <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> öğe verilerini sürüklemiş tutun.</span><span class="sxs-lookup"><span data-stu-id="1de9c-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="1de9c-281">| | Hata | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Olay | <xref:System.EventArgs>  |  *Genel*</span><span class="sxs-lookup"><span data-stu-id="1de9c-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="1de9c-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="1de9c-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="1de9c-283">*Pano*</span><span class="sxs-lookup"><span data-stu-id="1de9c-283">*Clipboard*</span></span><br><span data-ttu-id="1de9c-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="1de9c-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="1de9c-285">*Giriş*</span><span class="sxs-lookup"><span data-stu-id="1de9c-285">*Input*</span></span><br><span data-ttu-id="1de9c-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="1de9c-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="1de9c-287">*Medya*</span><span class="sxs-lookup"><span data-stu-id="1de9c-287">*Media*</span></span><br><span data-ttu-id="1de9c-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="1de9c-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="1de9c-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>olay adlarıyla olay bağımsız değişken türleri arasındaki eşlemeleri yapılandırmak için öznitelikleri tutar.</span><span class="sxs-lookup"><span data-stu-id="1de9c-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="1de9c-290">| | Odak | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="1de9c-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="1de9c-291">İçin destek içermez `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="1de9c-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="1de9c-292">| | Giriş | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Klavye | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Fare | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Fare işaretçisi | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Fare tekerleği | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | İlerleme durumu | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Dokunmatik | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="1de9c-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="1de9c-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>dokunarak duyarlı bir cihazdaki tek bir iletişim noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1de9c-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="1de9c-294">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="1de9c-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="1de9c-295">[ASP.NET Core başvuru kaynağındaki EventArgs sınıfları (DotNet/aspnetcore sürümü/3.1 dalı)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="1de9c-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="1de9c-296">[MDN Web belgeleri: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): hangi HTML ÖĞELERININ her Dom olayını destekledikleri hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="1de9c-297">Lambda ifadeleri</span><span class="sxs-lookup"><span data-stu-id="1de9c-297">Lambda expressions</span></span>

<span data-ttu-id="1de9c-298">[Lambda ifadeleri](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="1de9c-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="1de9c-299">Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="1de9c-300">Aşağıdaki örnek, her biri `UpdateHeading` Kullanıcı arabiriminde seçildiğinde bir olay bağımsız değişkeni ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) ve düğme numarası () geçiren üç düğme oluşturur `buttonNumber` :</span><span class="sxs-lookup"><span data-stu-id="1de9c-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="1de9c-301">Döngü **not** değişkenini ( `i` ) bir `for` döngüde doğrudan bir lambda ifadesinde kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="1de9c-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="1de9c-302">Aksi halde, tüm lambda ifadeleri tarafından `i` değeri tüm Lambdalar aynı olmasına neden olan değişken aynı değişken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1de9c-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="1de9c-303">Her zaman değerini yerel bir değişkende ( `buttonNumber` Önceki örnekte) yakalayın ve sonra kullanın.</span><span class="sxs-lookup"><span data-stu-id="1de9c-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="1de9c-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="1de9c-304">EventCallback</span></span>

<span data-ttu-id="1de9c-305">İç içe bileşenler içeren yaygın bir senaryo, bir alt bileşen olayı gerçekleştiğinde bir üst bileşenin yöntemini çalıştırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="1de9c-306">`onclick`Alt bileşende gerçekleşen bir olay yaygın kullanım durumdur.</span><span class="sxs-lookup"><span data-stu-id="1de9c-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="1de9c-307">Olayları bileşenler genelinde göstermek için bir kullanın <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="1de9c-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="1de9c-308">Bir üst bileşen bir alt bileşene geri çağırma yöntemi atayabilir <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="1de9c-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="1de9c-309">`ChildComponent`Örnek uygulamada (*Bileşenler/childcomponent. Razor*), bir düğmenin `onclick` işleyicisinin örnekten bir temsilci alacak şekilde nasıl ayarlandığını gösterir <xref:Microsoft.AspNetCore.Components.EventCallback> `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="1de9c-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="1de9c-310">,, <xref:Microsoft.AspNetCore.Components.EventCallback> `MouseEventArgs` `onclick` Bir çevre cihazından bir olay için uygun olan ile öğesine yazılır:</span><span class="sxs-lookup"><span data-stu-id="1de9c-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="1de9c-311">, `ParentComponent` Alt öğenin <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) metodunu kendi yöntemine ayarlar `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="1de9c-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="1de9c-312">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1de9c-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="1de9c-313">Düğme ' de seçildiğinde `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="1de9c-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="1de9c-314">`ParentComponent`Öğesinin `ShowMessage` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="1de9c-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="1de9c-315">`messageText`güncelleştirilir ve içinde görüntülenir `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="1de9c-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="1de9c-316">Geri çağırma yönteminde () [Statehaschanged](xref:blazor/lifecycle#state-changes) çağrısı gerekli değildir `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="1de9c-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="1de9c-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>alt `ParentComponent` Olaylar, alt öğe içinde yürütülen olay işleyicilerinde bileşen rerendering tetiklenmesi için otomatik olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="1de9c-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="1de9c-318"><xref:Microsoft.AspNetCore.Components.EventCallback>ve <xref:Microsoft.AspNetCore.Components.EventCallback%601> zaman uyumsuz temsilcilere izin verir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="1de9c-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601>kesin bir şekilde türdedir ve belirli bir bağımsız değişken türü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="1de9c-320"><xref:Microsoft.AspNetCore.Components.EventCallback>zayıf ve bağımsız değişken türüne izin veriyor.</span><span class="sxs-lookup"><span data-stu-id="1de9c-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="1de9c-321">Bir <xref:Microsoft.AspNetCore.Components.EventCallback> veya <xref:Microsoft.AspNetCore.Components.EventCallback%601> ile çağırın <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> ve şunu bekler <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="1de9c-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="1de9c-322"><xref:Microsoft.AspNetCore.Components.EventCallback> <xref:Microsoft.AspNetCore.Components.EventCallback%601> Olay işleme ve bağlama bileşeni parametreleri için ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="1de9c-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="1de9c-323">Kesin olarak belirlenmiş türü tercih edin <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="1de9c-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="1de9c-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601>bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="1de9c-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="1de9c-325">Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="1de9c-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="1de9c-326"><xref:Microsoft.AspNetCore.Components.EventCallback>Geri çağırmaya hiçbir değer geçirilmemişse kullanın.</span><span class="sxs-lookup"><span data-stu-id="1de9c-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="1de9c-327">Varsayılan eylemleri engelle</span><span class="sxs-lookup"><span data-stu-id="1de9c-327">Prevent default actions</span></span>

<span data-ttu-id="1de9c-328">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Bir olayın varsayılan eylemini engellemek için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1de9c-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="1de9c-329">Giriş cihazında bir anahtar seçildiğinde ve öğe odağı bir metin kutusunda olduğunda, bir tarayıcı normalde metin kutusunda anahtarın karakterini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="1de9c-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="1de9c-330">Aşağıdaki örnekte, Directive özniteliği belirtilerek varsayılan davranış engellenir `@onkeypress:preventDefault` .</span><span class="sxs-lookup"><span data-stu-id="1de9c-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="1de9c-331">Sayaç artar ve **+** anahtar `<input>` öğenin değerine yakalanmaz:</span><span class="sxs-lookup"><span data-stu-id="1de9c-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="1de9c-332">`@on{EVENT}:preventDefault`Özniteliği bir değer olmadan belirtmek ile eşdeğerdir `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="1de9c-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="1de9c-333">Özniteliğin değeri de bir ifade olabilir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="1de9c-334">Aşağıdaki örnekte, `shouldPreventDefault` `bool` ya da olarak ayarlanan bir alandır `true` `false` :</span><span class="sxs-lookup"><span data-stu-id="1de9c-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="1de9c-335">Varsayılan eylemi engellemek için bir olay işleyicisi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="1de9c-336">Olay işleyicisi ve varsayılan eylem senaryolarına bağımsız olarak bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1de9c-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="1de9c-337">Olay yaymayı durdur</span><span class="sxs-lookup"><span data-stu-id="1de9c-337">Stop event propagation</span></span>

<span data-ttu-id="1de9c-338">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Olay yaymayı durdurmak için Directive özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1de9c-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="1de9c-339">Aşağıdaki örnekte, onay kutusunun seçilmesi ikinci alt öğeden üst öğeye yayılan olay tıklamasını önler `<div>` `<div>` :</span><span class="sxs-lookup"><span data-stu-id="1de9c-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
