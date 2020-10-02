HTTP tanıtımları, yanıt gövdesi gönderildikten sonra gönderilmeleri dışında HTTP üst bilgilerine benzer. IIS ve HTTP.SYS için yalnızca HTTP/2 yanıt tanıtımları desteklenir.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

Yukarıdaki örnek kodda:

* `SupportsTrailers` Yanıt için tanıtımın desteklendiğinden emin olmanızı sağlar.
* `DeclareTrailer` verilen artbilgisi adını `Trailer` Yanıt üstbilgisine ekler. Yanıtın tanıtım listesini bildirmek isteğe bağlıdır, ancak önerilir. `DeclareTrailer`Çağrılırsa, yanıt üstbilgileri gönderilmeden önce gelmelidir.
* `AppendTrailer` treyleri ekler.
