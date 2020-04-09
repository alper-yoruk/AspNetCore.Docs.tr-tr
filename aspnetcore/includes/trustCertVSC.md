* <span data-ttu-id="8c113-101">Aşağıdaki komutu çalıştırarak HTTPS geliştirme sertifikasına güvenin:</span><span class="sxs-lookup"><span data-stu-id="8c113-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="8c113-102">Önceki komut Linux'ta çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8c113-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="8c113-103">Bir sertifikaya güvenmek için Linux dağıtımınızın belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="8c113-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="8c113-104">Önceki komut aşağıdaki iletişim kutusunu görüntüler:</span><span class="sxs-lookup"><span data-stu-id="8c113-104">The preceding command displays the following dialog:</span></span>

  ![Güvenlik uyarısı iletişim kutusu](~/getting-started/_static/cert.png)

* <span data-ttu-id="8c113-106">Geliştirme sertifikasına güvenmeyi kabul ederseniz **Evet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="8c113-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="8c113-107">Daha fazla bilgi için [ASP.NET Core HTTPS geliştirme sertifikasına güven'e](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) bakın.</span><span class="sxs-lookup"><span data-stu-id="8c113-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
