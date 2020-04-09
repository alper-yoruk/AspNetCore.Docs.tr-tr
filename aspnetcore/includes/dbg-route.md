## <a name="debug-diagnostics"></a><span data-ttu-id="ad2b8-101">Hata ayıklama tanılama</span><span class="sxs-lookup"><span data-stu-id="ad2b8-101">Debug diagnostics</span></span>

<span data-ttu-id="ad2b8-102">Ayrıntılı yönlendirme tanı çıktısı `Logging:LogLevel:Microsoft` için, '' olarak `Debug`ayarlayın</span><span class="sxs-lookup"><span data-stu-id="ad2b8-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="ad2b8-103">Örneğin, geliştirme ortamında uygulama *ayarlarını ayarlayın. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="ad2b8-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}