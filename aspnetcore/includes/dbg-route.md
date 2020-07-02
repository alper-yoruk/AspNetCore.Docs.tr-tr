## <a name="debug-diagnostics"></a>Hata ayıklama tanılaması

Ayrıntılı yönlendirme tanılama çıktısı için, `Logging:LogLevel:Microsoft` olarak ayarlayın `Debug` . Geliştirme ortamında, *appsettings.Development.jsüzerindeki*günlük düzeyini ayarlayın:

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
