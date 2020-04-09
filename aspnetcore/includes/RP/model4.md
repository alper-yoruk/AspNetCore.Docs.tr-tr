<a name="codegenerator"></a>Aşağıdaki tabloda ASP.NET Çekirdek kodu jeneratör parametreleri ayrıntılı olarak belirtin:

| Parametre               | Açıklama|
| ----------------- | ------------ |
| -m  | Modelin adı. |
| -dc  | Kullanılacak `DbContext` sınıf. |
| -udl | Varsayılan düzeni kullanın. |
| -outDir | Görünümleri oluşturmak için göreli çıktı klasörü yolu. |
| --referenceScriptLibraries | Sayfaları `_ValidationScriptsPartial` Edit ve Oluştur'a ekler |

`aspnet-codegenerator razorpage` Komutu `h` yardım almak için anahtarı kullanın:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Daha fazla bilgi için [dotnet aspnet-codegenerator'a](xref:fundamentals/tools/dotnet-aspnet-codegenerator)bakın.
