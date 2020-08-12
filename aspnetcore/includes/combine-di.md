<a name="csc"></a>

## <a name="combining-service-collection"></a>Hizmet toplamayı birleştirme

`ConfigureServices`Birkaç hizmet koleksiyonu içeren aşağıdakileri göz önünde bulundurun:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

İlgili kayıt grupları, Hizmetleri kaydetmek için bir genişletme yöntemine taşınabilir. Örneğin, Yapılandırma Hizmetleri aşağıdaki sınıfa eklenir:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Kalan hizmetler benzer bir sınıfa kaydedilir. Aşağıdaki, `ConfigureServices` Hizmetleri kaydetmek için yeni genişletme yöntemlerini kullanır:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Note:*** Her `services.Add{SERVICE_NAME}` uzantı yöntemi, Hizmetleri ekler ve potansiyel olarak yapılandırır. Örneğin, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> Görünümler IÇIN MVC denetleyicileri gereken hizmetleri ekler. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>gerekli hizmetleri Razor Pages ekler. Uygulamaların bu adlandırma kuralını izlemesini öneririz. Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini [Microsoft. Extensions. Dependencyınjection](/dotnet/api/microsoft.extensions.dependencyinjection) ad alanına yerleştirin.