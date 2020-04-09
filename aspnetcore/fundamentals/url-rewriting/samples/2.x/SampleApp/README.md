# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="e652c-101">ASP.NET Çekirdek URL Yeniden Yazma Örneği</span><span class="sxs-lookup"><span data-stu-id="e652c-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="e652c-102">Bu örnek, ASP.NET Core URL Rewriting Middleware kullanımını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="e652c-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="e652c-103">Uygulama, URL yönlendirme ve URL yeniden yazma seçeneklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="e652c-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="e652c-104">Örneği çalıştırırken, kurallardan biri istek URL'sine uygulandığında dosya dışı yanıtlar yeniden yazılmış veya yeniden yönlendirilen URL'yi döndürer.</span><span class="sxs-lookup"><span data-stu-id="e652c-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="e652c-105">XML ve metin dosyası örnekleri için, Statik Dosya Middleware istek URL'si ara yazılım tarafından yeniden yazıldıktan sonra dosyaya hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="e652c-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="e652c-106">Bu örnekteki örnekler</span><span class="sxs-lookup"><span data-stu-id="e652c-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="e652c-107">Başarı durum kodu: 302 (Bulundu)</span><span class="sxs-lookup"><span data-stu-id="e652c-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="e652c-108">Örnek (yönlendirme): **/redirect-rule/{capture_group}** **to/directed/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="e652c-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="e652c-109">Başarı durum kodu: 200 (Tamam)</span><span class="sxs-lookup"><span data-stu-id="e652c-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="e652c-110">Örnek (yeniden yazma): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="e652c-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="e652c-111">Başarı durum kodu: 302 (Bulundu)</span><span class="sxs-lookup"><span data-stu-id="e652c-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="e652c-112">Örnek (yönlendirme): **/apache-mod-rules-redirect/{capture_group}** **to/redirected?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="e652c-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="e652c-113">Başarı durum kodu: 200 (Tamam)</span><span class="sxs-lookup"><span data-stu-id="e652c-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="e652c-114">Örnek (yeniden yazma): **/iis-rules-rewrite/{capture_group}** **to/rewritten?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="e652c-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="e652c-115">Başarı durum kodu: 301 (Kalıcı olarak taşınır)</span><span class="sxs-lookup"><span data-stu-id="e652c-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="e652c-116">Örnek (yönlendirme): **/file.xml** to **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="e652c-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="e652c-117">Başarı durum kodu: 200 (Tamam)</span><span class="sxs-lookup"><span data-stu-id="e652c-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="e652c-118">Örnek (yeniden yazma): **/some_file.txt** **to /file.txt**</span><span class="sxs-lookup"><span data-stu-id="e652c-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="e652c-119">Başarı durum kodu: 301 (Kalıcı olarak taşınır)</span><span class="sxs-lookup"><span data-stu-id="e652c-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="e652c-120">Örnek (yönlendirme): **/image.png** to **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="e652c-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="e652c-121">Örnek (yönlendirme): **/image.jpg** için **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="e652c-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="e652c-122">PhysicalFileProvider kullanma</span><span class="sxs-lookup"><span data-stu-id="e652c-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="e652c-123">`IFileProvider` Ayrıca bir `PhysicalFileProvider` `AddApacheModRewrite()` ve `AddIISUrlRewrite()` yöntemleri geçmek için oluşturarak elde edebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="e652c-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="e652c-124">Güvenli yeniden yönlendirme uzantıları</span><span class="sxs-lookup"><span data-stu-id="e652c-124">Secure redirection extensions</span></span>

<span data-ttu-id="e652c-125">Bu örnek, güvenli yönlendirme yöntemlerini keşfetmeye yardımcı olmak için uygulamanın URL'leri (, `WebHostBuilder` `https://localhost:5001` `https://localhost`) ve test sertifikası *(testCert.pfx)* kullanmasını sağlayan yapılandırmayı içerir.</span><span class="sxs-lookup"><span data-stu-id="e652c-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="e652c-126">Sunucuzaten atanmış veya kullanımda port 443 `https://localhost` varsa, örnek&mdash;kestrel bağlantı noktasını `CreateWebHostBuilder` kullanabilirsiniz böylece sunucuda *Program.cs* dosyası veya unbind bağlantı noktası 443 yönteminde 443 `ListenOptions` için kaldırmak işe yaramazsa.</span><span class="sxs-lookup"><span data-stu-id="e652c-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="e652c-127">Yöntem</span><span class="sxs-lookup"><span data-stu-id="e652c-127">Method</span></span>                           | <span data-ttu-id="e652c-128">Durum Kodu</span><span class="sxs-lookup"><span data-stu-id="e652c-128">Status Code</span></span> |    <span data-ttu-id="e652c-129">Bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="e652c-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="e652c-130">301</span><span class="sxs-lookup"><span data-stu-id="e652c-130">301</span></span>     | <span data-ttu-id="e652c-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="e652c-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="e652c-132">302</span><span class="sxs-lookup"><span data-stu-id="e652c-132">302</span></span>     | <span data-ttu-id="e652c-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="e652c-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="e652c-134">301</span><span class="sxs-lookup"><span data-stu-id="e652c-134">301</span></span>     | <span data-ttu-id="e652c-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="e652c-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="e652c-136">301</span><span class="sxs-lookup"><span data-stu-id="e652c-136">301</span></span>     |    <span data-ttu-id="e652c-137">5001</span><span class="sxs-lookup"><span data-stu-id="e652c-137">5001</span></span>    |
