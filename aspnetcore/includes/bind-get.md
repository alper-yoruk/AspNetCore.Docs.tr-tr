> [!WARNING]
> Güvenlik nedenleriyle, `GET` istek verilerini sayfa modeli özelliklerine bağlamayı kabul etmeniz gerekir. Özelliklerle eşleştirmadan önce Kullanıcı girişini doğrulayın. `GET`Bağlama sırasında, sorgu dizesine veya rota değerlerine dayanan senaryolara yönelik adresleme yararlı olur.
>
> İsteklere bir özelliği bağlamak için `GET` [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) özniteliğinin `SupportsGet` özelliğini şu şekilde ayarlayın `true` :
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Daha fazla bilgi için bkz. [ASP.NET Core topluluk alışması: Get tartışmasına bağlama (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
