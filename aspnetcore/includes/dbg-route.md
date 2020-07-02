## <a name="debug-diagnostics"></a><span data-ttu-id="f3f7f-101">Hata ayıklama tanılaması</span><span class="sxs-lookup"><span data-stu-id="f3f7f-101">Debug diagnostics</span></span>

<span data-ttu-id="f3f7f-102">Ayrıntılı yönlendirme tanılama çıktısı için, `Logging:LogLevel:Microsoft` olarak ayarlayın `Debug` .</span><span class="sxs-lookup"><span data-stu-id="f3f7f-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="f3f7f-103">Geliştirme ortamında, *appsettings.Development.jsüzerindeki*günlük düzeyini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f3f7f-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
