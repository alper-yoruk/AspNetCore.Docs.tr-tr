# <a name="visual-studio"></a>[<span data-ttu-id="a4729-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4729-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4729-102">Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a4729-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a4729-103">Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a4729-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a4729-104">Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="a4729-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a4729-105">Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="a4729-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="a4729-106">Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="a4729-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a4729-107">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a4729-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="a4729-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4729-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="a4729-109">Hata ayıklama olmadan çalıştırmak için **Ctrl-F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a4729-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a4729-110">Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `http://localhost:5001`başlattı ve gezinir .</span><span class="sxs-lookup"><span data-stu-id="a4729-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="a4729-111">Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="a4729-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a4729-112">Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="a4729-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a4729-113">Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="a4729-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4729-114">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4729-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a4729-115">Visual Studio'dan hata ayıklama olmadan çalıştırmak için **Opt-Cmd-Return** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a4729-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="a4729-116">Alternatif olarak, menü çubuğuna gidin ve **Hata Ayıklama olmadan başlat>çalıştır'a**gidin.</span><span class="sxs-lookup"><span data-stu-id="a4729-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="a4729-117">Visual Studio [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı başlattı `http://localhost:5001`ve gezinir .</span><span class="sxs-lookup"><span data-stu-id="a4729-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---