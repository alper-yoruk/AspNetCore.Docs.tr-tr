> [!WARNING]
> Bir **catch-all** parametresi, yönlendirdeki bir [hata](https://github.com/dotnet/aspnetcore/issues/18677) nedeniyle yollarla yanlış bir şekilde eşleşmeyebilir. Bu hatadan etkilenen uygulamalar aşağıdaki özelliklere sahiptir:
>
> * Örneğin, bir catch-all rotası`{**slug}"`
> * Catch-all yolu eşleşmesi gereken isteklerle eşleşemez.
> * Diğer yolların kaldırılması, catch-all yolunun çalışmaya başlamasını sağlar.
>
> Bu hataya vurmakta olan örnekler için GitHub hataları [18677](https://github.com/dotnet/aspnetcore/issues/18677) ve [16579](https://github.com/dotnet/aspnetcore/issues/16579) bakın.
>
> Bu hata için bir katılım çözümü [.NET Core 3.1.301 SDK ve sonraki sürümlerde](https://dotnet.microsoft.com/download/dotnet-core/3.1)bulunur. Aşağıdaki kod, bu hatayı düzelten bir iç anahtar ayarlar:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```