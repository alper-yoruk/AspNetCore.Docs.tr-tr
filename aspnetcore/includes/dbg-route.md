## <a name="debug-diagnostics"></a>Hata ayıklama tanılama

Ayrıntılı yönlendirme tanı çıktısı `Logging:LogLevel:Microsoft` için, '' olarak `Debug`ayarlayın Örneğin, geliştirme ortamında uygulama *ayarlarını ayarlayın. Development.json*:

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