### <a name="use-sqlite-for-development-sql-server-for-production"></a>Geliştirme için SQLite'ı, üretim için SQL Server'ı kullanın

SQLite seçildiğinde, şablon oluşturulan kod geliştirmeye hazırdır. Aşağıdaki kod, Başlangıç'a nasıl enjekte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> ediletilir gösterir. `IWebHostEnvironment`böylece geliştirme `ConfigureServices` ve SQL Server üretimde SQLite kullanabilirsiniz enjekte edilir.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
