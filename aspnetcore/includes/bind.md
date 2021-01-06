İlgili yapılandırma değerlerini okumak için tercih edilen yol, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmaktır. Örneğin, aşağıdaki yapılandırma değerlerini okumak için:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Aşağıdaki sınıfı oluşturun `PositionOptions` :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Seçenekler sınıfı:

* Ortak parametresiz bir oluşturucuya sahip olmayan Özet olmamalıdır.
* Türün tüm genel okuma-yazma özellikleri bağlanır.
* Alanlar * değil _ bağlantılı **değildir**. Yukarıdaki kodda, `Position` bağlantılı değildir. `Position`Özelliği, `"Position"` sınıfı bir yapılandırma sağlayıcısına bağlarken, dizenin uygulamada sabit kodlanmış olması gerekmez.

Aşağıdaki kod:

_ Sınıfı bölümüne bağlamak için [Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) öğesini çağırır `PositionOptions` `Position` .
* `Position`Yapılandırma verilerini görüntüler.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) belirtilen türü bağlar ve döndürür. `ConfigurationBinder.Get<T>` , kullanmaktan daha uygun olabilir `ConfigurationBinder.Bind` . Aşağıdaki kod, sınıfıyla birlikte nasıl kullanılacağını gösterir `ConfigurationBinder.Get<T>` `PositionOptions` :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.

***Seçenek deseninin** _ 'i kullanılırken alternatif bir yaklaşım, `Position` bölümü bağlamak ve [bağımlılık ekleme hizmeti kapsayıcısına](xref:fundamentals/dependency-injection)eklemektir. Aşağıdaki kodda, `PositionOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> ve yapılandırmaya bağlanır:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Önceki kodu kullanarak, aşağıdaki kod konum seçeneklerini okur:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

Yukarıdaki kodda, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan ***değişiklikler okunamaz.*** Uygulama başladıktan sonra değişiklikleri okumak için [ıoptionssnapshot](xref:fundamentals/configuration/options#ios)kullanın.
