* `-F|--no-formatting`

  <span data-ttu-id="3499d-101">Durumu HTTP yanıt biçimlendirmesini belirten bir bayrak.</span><span class="sxs-lookup"><span data-stu-id="3499d-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="3499d-102">HTTP istek üst bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3499d-102">Sets an HTTP request header.</span></span> <span data-ttu-id="3499d-103">Aşağıdaki iki değer biçimi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="3499d-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="3499d-104">HTTP yanıt gövdesinin yazılması gereken bir dosyayı belirtir.</span><span class="sxs-lookup"><span data-stu-id="3499d-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="3499d-105">Örneğin, `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="3499d-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="3499d-106">Dosya yoksa oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3499d-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="3499d-107">HTTP yanıt üst bilgilerinin yazılması gereken bir dosyayı belirtir.</span><span class="sxs-lookup"><span data-stu-id="3499d-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="3499d-108">Örneğin, `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="3499d-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="3499d-109">Dosya yoksa oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3499d-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="3499d-110">Mevcut bir bayrak, HTTP yanıtının akışını mümkün bir şekilde sunar.</span><span class="sxs-lookup"><span data-stu-id="3499d-110">A flag whose presence enables streaming of the HTTP response.</span></span>
