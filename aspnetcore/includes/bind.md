<span data-ttu-id="f26dd-101">İlgili yapılandırma değerlerini okumak için tercih edilen yol, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="f26dd-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f26dd-102">Örneğin, aşağıdaki yapılandırma değerlerini okumak için:</span><span class="sxs-lookup"><span data-stu-id="f26dd-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="f26dd-103">Aşağıdaki sınıfı oluşturun `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="f26dd-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="f26dd-104">Seçenekler sınıfı:</span><span class="sxs-lookup"><span data-stu-id="f26dd-104">An options class:</span></span>

* <span data-ttu-id="f26dd-105">Ortak parametresiz bir oluşturucuya sahip olmayan Özet olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="f26dd-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="f26dd-106">Türün tüm genel okuma-yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="f26dd-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="f26dd-107">Alanlar bağlanmadı ***.***</span><span class="sxs-lookup"><span data-stu-id="f26dd-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="f26dd-108">Yukarıdaki kodda, `Position` bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="f26dd-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="f26dd-109">`Position`Özelliği, `"Position"` sınıfı bir yapılandırma sağlayıcısına bağlarken, dizenin uygulamada sabit kodlanmış olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f26dd-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="f26dd-110">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="f26dd-110">The following code:</span></span>

* <span data-ttu-id="f26dd-111">Sınıfı bölümüne bağlamak için [Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) ' i çağırır `PositionOptions` `Position` .</span><span class="sxs-lookup"><span data-stu-id="f26dd-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="f26dd-112">`Position`Yapılandırma verilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f26dd-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="f26dd-113">Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.</span><span class="sxs-lookup"><span data-stu-id="f26dd-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="f26dd-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="f26dd-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="f26dd-115">`ConfigurationBinder.Get<T>`, kullanmaktan daha uygun olabilir `ConfigurationBinder.Bind` .</span><span class="sxs-lookup"><span data-stu-id="f26dd-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="f26dd-116">Aşağıdaki kod, sınıfıyla birlikte nasıl kullanılacağını gösterir `ConfigurationBinder.Get<T>` `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="f26dd-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="f26dd-117">Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.</span><span class="sxs-lookup"><span data-stu-id="f26dd-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="f26dd-118">***Seçenekler deseninin*** kullanılmasına yönelik alternatif bir yaklaşım, `Position` bölümü bağlamak ve [bağımlılık ekleme hizmeti kapsayıcısına](xref:fundamentals/dependency-injection)eklemektir.</span><span class="sxs-lookup"><span data-stu-id="f26dd-118">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f26dd-119">Aşağıdaki kodda, `PositionOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="f26dd-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="f26dd-120">Önceki kodu kullanarak, aşağıdaki kod konum seçeneklerini okur:</span><span class="sxs-lookup"><span data-stu-id="f26dd-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="f26dd-121">Yukarıdaki kodda, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan ***değişiklikler okunamaz.***</span><span class="sxs-lookup"><span data-stu-id="f26dd-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="f26dd-122">Uygulama başladıktan sonra değişiklikleri okumak için [ıoptionssnapshot](xref:fundamentals/configuration/options#ios)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f26dd-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
