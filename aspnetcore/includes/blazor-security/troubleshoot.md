## <a name="troubleshoot"></a><span data-ttu-id="0397f-101">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="0397f-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="0397f-102">Çerezler ve site verileri</span><span class="sxs-lookup"><span data-stu-id="0397f-102">Cookies and site data</span></span>

<span data-ttu-id="0397f-103">Çerezler ve site verileri uygulama güncelleştirmeleri arasında devam edebilir ve test ve sorun giderme işlemlerini etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="0397f-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="0397f-104">Uygulama kodu değişiklikleri, sağlayıcı yla veya sağlayıcı uygulama yapılandırma değişiklikleri yaparken aşağıdakileri temizleyin:</span><span class="sxs-lookup"><span data-stu-id="0397f-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="0397f-105">Kullanıcı oturum açma çerezleri</span><span class="sxs-lookup"><span data-stu-id="0397f-105">User sign-in cookies</span></span>
* <span data-ttu-id="0397f-106">Uygulama çerezleri</span><span class="sxs-lookup"><span data-stu-id="0397f-106">App cookies</span></span>
* <span data-ttu-id="0397f-107">Önbelleğe alınmış ve depolanmış site verileri</span><span class="sxs-lookup"><span data-stu-id="0397f-107">Cached and stored site data</span></span>

<span data-ttu-id="0397f-108">Kalan tanımlama bilgilerinin ve site verilerinin sınama ve sorun giderme ile karışmasını önlemek için bir yaklaşım:</span><span class="sxs-lookup"><span data-stu-id="0397f-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="0397f-109">Tarayıcı her kapatıldığında tüm çerez ve site verilerini silmek için yapılandırabileceğiniz bir tarayıcı kullanın.</span><span class="sxs-lookup"><span data-stu-id="0397f-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="0397f-110">Uygulamayı, test kullanıcısında veya sağlayıcı yapılandırmasına yapılan herhangi bir değişiklik arasında tarayıcıyı kapatın.</span><span class="sxs-lookup"><span data-stu-id="0397f-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="0397f-111">Sunucu uygulamasını çalıştırın</span><span class="sxs-lookup"><span data-stu-id="0397f-111">Run the Server app</span></span>

<span data-ttu-id="0397f-112">Barındırılan bir Blazor uygulamasını test ederken ve sorun giderirken, uygulamayı **Sunucu** projesinden çalıştırdığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="0397f-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="0397f-113">Örneğin Visual Studio'da, uygulamaya aşağıdaki yaklaşımlardan herhangi biriyle başlamadan önce Sunucu projesinin **Solution Explorer'da** vurgulandığına onaylayın:</span><span class="sxs-lookup"><span data-stu-id="0397f-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="0397f-114">**Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0397f-114">Select the **Run** button.</span></span>
* <span data-ttu-id="0397f-115">Menüden **Hata** > Ayıklama Başlat hata**ayıklama'yı** kullanın.</span><span class="sxs-lookup"><span data-stu-id="0397f-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="0397f-116"><kbd>F5 tuşuna</kbd>basın.</span><span class="sxs-lookup"><span data-stu-id="0397f-116">Press <kbd>F5</kbd>.</span></span>
