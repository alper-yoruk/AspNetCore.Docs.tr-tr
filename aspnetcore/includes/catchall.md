> [!WARNING]
> <span data-ttu-id="c4cd8-101">Bir **catch-all** parametresi, yönlendirdeki bir [hata](https://github.com/dotnet/aspnetcore/issues/18677) nedeniyle yollarla yanlış bir şekilde eşleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="c4cd8-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="c4cd8-102">Bu hatadan etkilenen uygulamalar aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="c4cd8-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="c4cd8-103">Örneğin, bir catch-all rotası`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="c4cd8-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="c4cd8-104">Catch-all yolu eşleşmesi gereken isteklerle eşleşemez.</span><span class="sxs-lookup"><span data-stu-id="c4cd8-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="c4cd8-105">Diğer yolların kaldırılması, catch-all yolunun çalışmaya başlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4cd8-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="c4cd8-106">Bu hataya vurmakta olan örnekler için GitHub hataları [18677](https://github.com/dotnet/aspnetcore/issues/18677) ve [16579](https://github.com/dotnet/aspnetcore/issues/16579) bakın.</span><span class="sxs-lookup"><span data-stu-id="c4cd8-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="c4cd8-107">Bu hata için bir katılım çözümü [.NET Core 3.1.301 SDK ve sonraki sürümlerde](https://dotnet.microsoft.com/download/dotnet-core/3.1)bulunur.</span><span class="sxs-lookup"><span data-stu-id="c4cd8-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="c4cd8-108">Aşağıdaki kod, bu hatayı düzelten bir iç anahtar ayarlar:</span><span class="sxs-lookup"><span data-stu-id="c4cd8-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```