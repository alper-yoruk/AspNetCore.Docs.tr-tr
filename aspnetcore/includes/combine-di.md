<a name="csc"></a>

`ConfigureServices`Hizmetleri kaydeden ve seçenekleri yapılandıran aşağıdaki yöntemi göz önünde bulundurun:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

İlgili kayıt grupları, Hizmetleri kaydetmek için bir genişletme yöntemine taşınabilir. Örneğin, Yapılandırma Hizmetleri aşağıdaki sınıfa eklenir:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Kalan hizmetler benzer bir sınıfa kaydedilir. Aşağıdaki `ConfigureServices` Yöntem, Hizmetleri kaydetmek için yeni genişletme yöntemlerini kullanır:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Note:_** Her `services.Add{GROUP_NAME}` uzantı yöntemi, Hizmetleri ekler ve potansiyel olarak yapılandırır. Örneğin, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> görünümler için gereken HIZMETLERI MVC denetleyicileri ekler ve <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> Razor Pages gereken hizmetleri ekler. Uygulamaların bu adlandırma kuralını izlemesini öneririz. <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>Hizmet kaydı gruplarını kapsüllemek için uzantı yöntemlerini ad alanına yerleştirin.
