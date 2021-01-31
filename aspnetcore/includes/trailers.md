<span data-ttu-id="9d22f-101">HTTP tanıtımları, yanıt gövdesi gönderildikten sonra gönderilmeleri dışında HTTP üst bilgilerine benzer.</span><span class="sxs-lookup"><span data-stu-id="9d22f-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="9d22f-102">IIS ve HTTP.sys için yalnızca HTTP/2 yanıt tanıtımları desteklenir.</span><span class="sxs-lookup"><span data-stu-id="9d22f-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="9d22f-103">Yukarıdaki örnek kodda:</span><span class="sxs-lookup"><span data-stu-id="9d22f-103">In the preceding example code:</span></span>

* <span data-ttu-id="9d22f-104">`SupportsTrailers` Yanıt için tanıtımın desteklendiğinden emin olmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="9d22f-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="9d22f-105">`DeclareTrailer` verilen artbilgisi adını `Trailer` Yanıt üstbilgisine ekler.</span><span class="sxs-lookup"><span data-stu-id="9d22f-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="9d22f-106">Yanıtın tanıtım listesini bildirmek isteğe bağlıdır, ancak önerilir.</span><span class="sxs-lookup"><span data-stu-id="9d22f-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="9d22f-107">`DeclareTrailer`Çağrılırsa, yanıt üstbilgileri gönderilmeden önce gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="9d22f-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="9d22f-108">`AppendTrailer` treyleri ekler.</span><span class="sxs-lookup"><span data-stu-id="9d22f-108">`AppendTrailer` appends the trailer.</span></span>
