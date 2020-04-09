> [!WARNING]
> <span data-ttu-id="7464a-101">Güvenlik nedenleriyle, istek verilerini `GET` sayfa modeli özelliklerine bağlamayı tercih etmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="7464a-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="7464a-102">Özelliklerle eşlemeden önce kullanıcı girişini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7464a-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="7464a-103">Sorgu dizesi veya rota değerlerine dayanan senaryoları ele alırken bağlamayı `GET` seçmenin yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="7464a-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="7464a-104">İsteklere `GET` bir özelliği bağlamak için, özniteliğin [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) `true` `SupportsGet` özelliğini şu şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7464a-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="7464a-105">Daha fazla bilgi için [Core Community Standup ASP.NET: GET tartışmasını (YouTube) bağlayın.](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s)</span><span class="sxs-lookup"><span data-stu-id="7464a-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
