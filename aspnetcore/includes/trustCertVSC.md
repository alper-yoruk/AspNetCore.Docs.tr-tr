* Aşağıdaki komutu çalıştırarak HTTPS geliştirme sertifikasına güvenin:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Yukarıdaki komut Linux üzerinde çalışmaz. Bir sertifikaya güvenmek için Linux dağıtım belgelerine bakın.

  Yukarıdaki komutta aşağıdaki iletişim kutusu görüntülenir:

  ![Güvenlik Uyarısı iletişim kutusu](~/getting-started/_static/cert.png)

* Geliştirme sertifikasına güvenmeyi kabul ediyorsanız **Evet** ' i seçin.

  Daha fazla bilgi için bkz. [ASP.NET Core https geliştirme sertifikasına güvenin](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .
  
