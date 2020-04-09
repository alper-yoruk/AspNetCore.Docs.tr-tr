## <a name="share-interop-code-in-a-class-library"></a>Sınıf kitaplığında interop kodunu paylaşma

JS interop kodu, kodu NuGet paketinde paylaşmanızı sağlayan bir sınıf kitaplığına eklenebilir.

Sınıf kitaplığı, yerleşik derlemeye JavaScript kaynaklarını katıştırma işlemlerini işler. JavaScript dosyaları *wwwroot* klasörüne yerleştirilir. Takım, kitaplık oluşturulurken kaynakları gömme yi halletmekle ilgilenir.

Yerleşik NuGet paketi, uygulamanın proje dosyasında herhangi bir NuGet paketine başvurulduğu gibi başvurulan. Paket geri yüklendikten sonra, uygulama kodu JavaScript'e C# gibi çağrıyapabilir.

Daha fazla bilgi için bkz. <xref:blazor/class-libraries>.
