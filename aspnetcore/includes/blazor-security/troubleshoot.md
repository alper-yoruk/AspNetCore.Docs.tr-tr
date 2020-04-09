## <a name="troubleshoot"></a>Sorun giderme

### <a name="cookies-and-site-data"></a>Çerezler ve site verileri

Çerezler ve site verileri uygulama güncelleştirmeleri arasında devam edebilir ve test ve sorun giderme işlemlerini etkileyebilir. Uygulama kodu değişiklikleri, sağlayıcı yla veya sağlayıcı uygulama yapılandırma değişiklikleri yaparken aşağıdakileri temizleyin:

* Kullanıcı oturum açma çerezleri
* Uygulama çerezleri
* Önbelleğe alınmış ve depolanmış site verileri

Kalan tanımlama bilgilerinin ve site verilerinin sınama ve sorun giderme ile karışmasını önlemek için bir yaklaşım:

* Tarayıcı her kapatıldığında tüm çerez ve site verilerini silmek için yapılandırabileceğiniz bir tarayıcı kullanın.
* Uygulamayı, test kullanıcısında veya sağlayıcı yapılandırmasına yapılan herhangi bir değişiklik arasında tarayıcıyı kapatın.

### <a name="run-the-server-app"></a>Sunucu uygulamasını çalıştırın

Barındırılan bir Blazor uygulamasını test ederken ve sorun giderirken, uygulamayı **Sunucu** projesinden çalıştırdığınızdan emin olun. Örneğin Visual Studio'da, uygulamaya aşağıdaki yaklaşımlardan herhangi biriyle başlamadan önce Sunucu projesinin **Solution Explorer'da** vurgulandığına onaylayın:

* **Çalıştır** düğmesini seçin.
* Menüden **Hata** > Ayıklama Başlat hata**ayıklama'yı** kullanın.
* <kbd>F5 tuşuna</kbd>basın.
