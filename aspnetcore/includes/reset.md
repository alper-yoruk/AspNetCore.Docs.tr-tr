<span data-ttu-id="d8d33-101">Sıfırlama, sunucunun belirtilen bir hata kodu ile HTTP/2 isteğini sıfırlamasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d8d33-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="d8d33-102">Bir sıfırlama isteği durdurulan kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="d8d33-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="d8d33-103">`Reset` Yukarıdaki kod örneğinde `INTERNAL_ERROR` hata kodu belirtilir.</span><span class="sxs-lookup"><span data-stu-id="d8d33-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="d8d33-104">HTTP/2 hata kodları hakkında daha fazla bilgi için [http/2 belirtim hata kodu bölümünü](https://tools.ietf.org/html/rfc7540#page-50)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="d8d33-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>