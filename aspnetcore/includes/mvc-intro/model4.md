Aşağıdaki tabloda ASP.NET Çekirdek kodu jeneratör parametreleri ayrıntılı olarak belirtin:

| Parametre               | Açıklama|
| ----------------- | ------------ |
| -m  | Modelin adı. |
| -dc  | Veri bağlamı. |
| -udl | Varsayılan düzeni kullanın. |
| --relativeFolderPath | Dosyaları oluşturmak için göreli çıktı klasörü yolu. |
| --useDefaultLayout | Görünümler için varsayılan düzen kullanılmalıdır. |
| --referenceScriptLibraries | Sayfaları `_ValidationScriptsPartial` Edit ve Oluştur'a ekler |

`aspnet-codegenerator controller` Komutu `h` yardım almak için anahtarı kullanın:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Daha fazla bilgi için [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
