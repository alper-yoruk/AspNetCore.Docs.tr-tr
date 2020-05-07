Sunucu Blazor sunucu uygulamaları canlı. Diğer bir deyişle, aynı işlem içinde barındırılan birden çok uygulama vardır. Her uygulama oturumunda, Blazor kendi dı kapsayıcı kapsamı ile bir devre başlatır. Bu, kapsamlı hizmetlerin Blazor oturumu başına benzersiz olduğu anlamına gelir.

> [!WARNING]
> Bu durum, çok sayıda kullanıcı durumunun sızması gibi güvenlik açıklarını ortaya çıkaracak şekilde, aşırı bakım alınmadığı sürece aynı sunucu paylaşma durumunda uygulamalar önermemektedir.

Blazor uygulamalarında durum bilgisi olan Singleton Hizmetleri, özellikle kendisi için tasarlandıklarında kullanabilirsiniz. Örneğin, belirli bir girdiye erişmek için bir anahtar gerektirdiğinden bir bellek önbelleğinin tek bir şekilde kullanılması, kullanıcıların hangi önbellek anahtarlarının kullanıldıklarını denetleyemediği varsayılarak, bu bir değer olarak kullanılabilir.

**Ek olarak, güvenlik nedenleriyle, Blazor Apps içinde kullanmamalıdır <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> .** Blazor uygulamaları ASP.NET Core işlem hattı bağlamı dışında çalışır ve <xref:Microsoft.AspNetCore.Http.HttpContext> içinde kullanılabilir garanti edilmez <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, ya da Blazor uygulamasını Başlatan bağlamını tutan garanti edilir.

İstek durumunu Blazor uygulamasına geçirmek için önerilen yol, uygulamanın ilk işlemede kök bileşene parametreler kullanmaktır:

* Blazor uygulamasına geçirmek istediğiniz tüm verileri içeren bir sınıf tanımlayın.
* Bu verileri Razor sayfasından, <xref:Microsoft.AspNetCore.Http.HttpContext> bu sırada kullanılabilir olan ' i kullanarak doldurun.
* Verileri Blazor uygulamasına kök bileşene (uygulama) bir parametre olarak geçirin.
* Uygulamaya geçirilen verileri tutmak için kök bileşende bir parametre tanımlayın.
* Uygulama içinde kullanıcıya özgü verileri kullanın; Alternatif olarak, bu verileri uygulama genelinde kullanılabilmesi için içindeki <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> kapsamlı bir hizmete kopyalayın.

Daha fazla bilgi ve örnek kod için bkz <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>..
