Sıfırlama, sunucunun belirtilen bir hata kodu ile HTTP/2 isteğini sıfırlamasına izin verir. Bir sıfırlama isteği durdurulan kabul edilir.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` Yukarıdaki kod örneğinde `INTERNAL_ERROR` hata kodu belirtilir. HTTP/2 hata kodları hakkında daha fazla bilgi için [http/2 belirtim hata kodu bölümünü](https://tools.ietf.org/html/rfc7540#page-50)ziyaret edin.