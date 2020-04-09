* Aşağıdaki komutu çalıştırarak HTTPS geliştirme sertifikasına güvenin:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Önceki komut Linux'ta çalışmaz. Bir sertifikaya güvenmek için Linux dağıtımınızın belgelerine bakın.

  Önceki komut aşağıdaki iletişim kutusunu görüntüler:

  ![Güvenlik uyarısı iletişim kutusu](~/getting-started/_static/cert.png)

* Geliştirme sertifikasına güvenmeyi kabul ederseniz **Evet'i** seçin.

  Daha fazla bilgi için [ASP.NET Core HTTPS geliştirme sertifikasına güven'e](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) bakın.
  
