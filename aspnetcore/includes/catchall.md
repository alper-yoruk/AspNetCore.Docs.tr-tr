> [!WARNING]
> <span data-ttu-id="8bc9f-101">Bir **catch-all** parametresi, yönlendirdeki bir [hata](https://github.com/dotnet/aspnetcore/issues/18677) nedeniyle yollarla yanlış bir şekilde eşleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="8bc9f-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="8bc9f-102">Bu hatadan etkilenen uygulamalar aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="8bc9f-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="8bc9f-103">Örneğin, bir catch-all rotası`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="8bc9f-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="8bc9f-104">Catch-all yolu eşleşmesi gereken isteklerle eşleşemez.</span><span class="sxs-lookup"><span data-stu-id="8bc9f-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="8bc9f-105">Diğer yolların kaldırılması, catch-all yolunun çalışmaya başlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="8bc9f-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="8bc9f-106">Bu hataya vurmakta olan örnekler için GitHub hataları [18677](https://github.com/dotnet/aspnetcore/issues/18677) ve [16579](https://github.com/dotnet/aspnetcore/issues/16579) bakın.</span><span class="sxs-lookup"><span data-stu-id="8bc9f-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="8bc9f-107">Bu hata için bir katılım çözümü planlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8bc9f-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="8bc9f-108">Bu belge, düzeltme eki serbest bırakıldığında güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8bc9f-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="8bc9f-109">Düzeltme Eki bırakıldığında, aşağıdaki kod bu hatayı düzelten bir iç anahtar ayarlar:</span><span class="sxs-lookup"><span data-stu-id="8bc9f-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```