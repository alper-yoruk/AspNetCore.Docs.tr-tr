> [!WARNING]
> <span data-ttu-id="f7d87-101">Güvenlik nedenleriyle, `GET` istek verilerini sayfa modeli özelliklerine bağlamayı kabul etmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f7d87-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="f7d87-102">Özelliklerle eşleştirmadan önce Kullanıcı girişini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f7d87-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="f7d87-103">`GET`Bağlama sırasında, sorgu dizesine veya rota değerlerine dayanan senaryolara yönelik adresleme yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="f7d87-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="f7d87-104">İsteklere bir özelliği bağlamak için `GET` [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) özniteliğinin `SupportsGet` özelliğini şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="f7d87-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="f7d87-105">Daha fazla bilgi için bkz. [ASP.NET Core topluluk alışması: Get tartışmasına bağlama (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="f7d87-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
