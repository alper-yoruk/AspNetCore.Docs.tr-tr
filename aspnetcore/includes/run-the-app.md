# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın. Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır. Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Hata ayıklama olmadan çalıştırmak için **Ctrl-F5** tuşuna basın.

  Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `http://localhost:5001`başlattı ve gezinir . Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır. Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.

  
# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Visual Studio'dan hata ayıklama olmadan çalıştırmak için **Opt-Cmd-Return** tuşuna basın. Alternatif olarak, menü çubuğuna gidin ve **Hata Ayıklama olmadan başlat>çalıştır'a**gidin.

  Visual Studio [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı başlattı `http://localhost:5001`ve gezinir .

<!-- End of VS tabs -->

---