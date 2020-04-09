> [!WARNING]
> Güvenlik nedenleriyle, istek verilerini `GET` sayfa modeli özelliklerine bağlamayı tercih etmeniz gerekir. Özelliklerle eşlemeden önce kullanıcı girişini doğrulayın. Sorgu dizesi veya rota değerlerine dayanan senaryoları ele alırken bağlamayı `GET` seçmenin yararlıdır.
>
> İsteklere `GET` bir özelliği bağlamak için, özniteliğin [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) `true` `SupportsGet` özelliğini şu şekilde ayarlayın:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Daha fazla bilgi için [Core Community Standup ASP.NET: GET tartışmasını (YouTube) bağlayın.](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s)
