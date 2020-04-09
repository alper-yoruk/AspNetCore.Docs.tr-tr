# <a name="aspnet-core-url-rewriting-sample"></a>ASP.NET Çekirdek URL Yeniden Yazma Örneği

Bu örnek, ASP.NET Core URL Rewriting Middleware kullanımını göstermektedir. Uygulama, URL yönlendirme ve URL yeniden yazma seçeneklerini gösterir.

Örneği çalıştırırken, kurallardan biri istek URL'sine uygulandığında dosya dışı yanıtlar yeniden yazılmış veya yeniden yönlendirilen URL'yi döndürer. XML ve metin dosyası örnekleri için, Statik Dosya Middleware istek URL'si ara yazılım tarafından yeniden yazıldıktan sonra dosyaya hizmet eder.

## <a name="examples-in-this-sample"></a>Bu örnekteki örnekler

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Başarı durum kodu: 302 (Bulundu)
  - Örnek (yönlendirme): **/redirect-rule/{capture_group}** **to/directed/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Başarı durum kodu: 200 (Tamam)
  - Örnek (yeniden yazma): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Başarı durum kodu: 302 (Bulundu)
  - Örnek (yönlendirme): **/apache-mod-rules-redirect/{capture_group}** **to/redirected?id={capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Başarı durum kodu: 200 (Tamam)
  - Örnek (yeniden yazma): **/iis-rules-rewrite/{capture_group}** **to/rewritten?id={capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Başarı durum kodu: 301 (Kalıcı olarak taşınır)
  - Örnek (yönlendirme): **/file.xml** to **/xmlfiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Başarı durum kodu: 200 (Tamam)
  - Örnek (yeniden yazma): **/some_file.txt** **to /file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Başarı durum kodu: 301 (Kalıcı olarak taşınır)
  - Örnek (yönlendirme): **/image.png** to **/png-images/image.png**
  - Örnek (yönlendirme): **/image.jpg** için **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>PhysicalFileProvider kullanma

`IFileProvider` Ayrıca bir `PhysicalFileProvider` `AddApacheModRewrite()` ve `AddIISUrlRewrite()` yöntemleri geçmek için oluşturarak elde edebilirsiniz:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Güvenli yeniden yönlendirme uzantıları

Bu örnek, güvenli yönlendirme yöntemlerini keşfetmeye yardımcı olmak için uygulamanın URL'leri (, `WebHostBuilder` `https://localhost:5001` `https://localhost`) ve test sertifikası *(testCert.pfx)* kullanmasını sağlayan yapılandırmayı içerir. Sunucuzaten atanmış veya kullanımda port 443 `https://localhost` varsa, örnek&mdash;kestrel bağlantı noktasını `CreateWebHostBuilder` kullanabilirsiniz böylece sunucuda *Program.cs* dosyası veya unbind bağlantı noktası 443 yönteminde 443 `ListenOptions` için kaldırmak işe yaramazsa.

| Yöntem                           | Durum Kodu |    Bağlantı noktası    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | null (465) |
| `.AddRedirectToHttps()`          |     302     | null (465) |
| `.AddRedirectToHttps(301)`       |     301     | null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
