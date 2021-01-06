# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="b3bca-101">ASP.NET Core URL yeniden yazma örneği</span><span class="sxs-lookup"><span data-stu-id="b3bca-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="b3bca-102">Bu örnek ASP.NET Core URL yeniden yazma ara yazılımı kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="b3bca-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="b3bca-103">Uygulama, URL yeniden yönlendirme ve URL yeniden yazma seçeneklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b3bca-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="b3bca-104">Örneği çalıştırırken dosya olmayan yanıtlar, kurallardan biri bir istek URL 'sine uygulandığında yeniden yazan veya yeniden yönlendirilen URL 'YI döndürür.</span><span class="sxs-lookup"><span data-stu-id="b3bca-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="b3bca-105">XML ve metin dosyası örnekleri için, statik dosya ara yazılımı, istek URL 'SI, ara yazılım tarafından yeniden alındıktan sonra dosyayı hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="b3bca-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="b3bca-106">Bu örnekteki örnekler</span><span class="sxs-lookup"><span data-stu-id="b3bca-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="b3bca-107">Başarı durum kodu: 302 (bulundu)</span><span class="sxs-lookup"><span data-stu-id="b3bca-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="b3bca-108">Örnek (yeniden yönlendirme): **/redirect-Rule/{capture_group}** - **/redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="b3bca-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="b3bca-109">Başarı durum kodu: 200 (Tamam)</span><span class="sxs-lookup"><span data-stu-id="b3bca-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="b3bca-110">Örnek (yeniden yazma): **/rewrite-Rule/{capture_group_1}/{capture_group_2}** için **/yeniden yazıldı? var1 = {capture_group_1} &var2 = {capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="b3bca-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="b3bca-111">Başarı durum kodu: 302 (bulundu)</span><span class="sxs-lookup"><span data-stu-id="b3bca-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="b3bca-112">Örnek (Redirect): **/apache-mod-rules-redirect/{capture_group}** **/yeniden yönlendirme için? ıd = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="b3bca-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="b3bca-113">Başarı durum kodu: 200 (Tamam)</span><span class="sxs-lookup"><span data-stu-id="b3bca-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="b3bca-114">Örnek (yeniden yazma): **/iis-rules-rewrite/{capture_group}** için **/yeniden yazıldı? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="b3bca-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="b3bca-115">Başarı durum kodu: 301 (kalıcı olarak taşındı)</span><span class="sxs-lookup"><span data-stu-id="b3bca-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="b3bca-116">Örnek (yeniden yönlendirme): **/file.xml** **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="b3bca-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="b3bca-117">Başarı durum kodu: 200 (Tamam)</span><span class="sxs-lookup"><span data-stu-id="b3bca-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="b3bca-118">Örnek (yeniden yazma): **/some_file.txt** **/file.txt**</span><span class="sxs-lookup"><span data-stu-id="b3bca-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="b3bca-119">Başarı durum kodu: 301 (kalıcı olarak taşındı)</span><span class="sxs-lookup"><span data-stu-id="b3bca-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="b3bca-120">Örnek (yeniden yönlendirme): **/image.png** **/PNG-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="b3bca-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="b3bca-121">Örnek (yeniden yönlendirme): **/image.jpg** **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="b3bca-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="b3bca-122">PhysicalFileProvider kullanma</span><span class="sxs-lookup"><span data-stu-id="b3bca-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="b3bca-123">Ayrıca `IFileProvider` `PhysicalFileProvider` , ve yöntemlerine geçirilecek bir oluşturarak elde edebilirsiniz `AddApacheModRewrite()` `AddIISUrlRewrite()` :</span><span class="sxs-lookup"><span data-stu-id="b3bca-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="b3bca-124">Güvenli yeniden yönlendirme uzantıları</span><span class="sxs-lookup"><span data-stu-id="b3bca-124">Secure redirection extensions</span></span>

<span data-ttu-id="b3bca-125">Bu örnek `WebHostBuilder` `https://localhost:5001` `https://localhost` , güvenli yeniden yönlendirme yöntemlerini keşfetmeye yardımcı olmak üzere, uygulamanın URL 'leri (,) ve bir test sertifikası (*testCert. pfx*) kullanması için yapılandırma içerir.</span><span class="sxs-lookup"><span data-stu-id="b3bca-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="b3bca-126">Sunucuda 443 numaralı bağlantı noktası zaten varsa veya kullanımda ise, `https://localhost` örnek çalışmıyor &mdash; `ListenOptions` program.cs dosyası yönteminde bağlantı noktası 443 Için `CreateWebHostBuilder` ya da Kestrel bağlantı noktasını kullanabilmesi  için sunucudaki bağlantı noktası 443 bağlantısını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b3bca-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="b3bca-127">Yöntem</span><span class="sxs-lookup"><span data-stu-id="b3bca-127">Method</span></span>                           | <span data-ttu-id="b3bca-128">Durum Kodu</span><span class="sxs-lookup"><span data-stu-id="b3bca-128">Status Code</span></span> |    <span data-ttu-id="b3bca-129">Bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="b3bca-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="b3bca-130">301</span><span class="sxs-lookup"><span data-stu-id="b3bca-130">301</span></span>     | <span data-ttu-id="b3bca-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="b3bca-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="b3bca-132">302</span><span class="sxs-lookup"><span data-stu-id="b3bca-132">302</span></span>     | <span data-ttu-id="b3bca-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="b3bca-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="b3bca-134">301</span><span class="sxs-lookup"><span data-stu-id="b3bca-134">301</span></span>     | <span data-ttu-id="b3bca-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="b3bca-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="b3bca-136">301</span><span class="sxs-lookup"><span data-stu-id="b3bca-136">301</span></span>     |    <span data-ttu-id="b3bca-137">5001</span><span class="sxs-lookup"><span data-stu-id="b3bca-137">5001</span></span>    |
