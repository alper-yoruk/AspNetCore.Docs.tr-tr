* <span data-ttu-id="8d924-101">Aşağıdaki komutu çalıştırarak HTTPS geliştirme sertifikasına güvenin:</span><span class="sxs-lookup"><span data-stu-id="8d924-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="8d924-102">Yukarıdaki komut Linux üzerinde çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8d924-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="8d924-103">Bir sertifikaya güvenmek için Linux dağıtım belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="8d924-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="8d924-104">Yukarıdaki komutta aşağıdaki iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="8d924-104">The preceding command displays the following dialog:</span></span>

  ![Güvenlik Uyarısı iletişim kutusu](~/getting-started/_static/cert.png)

* <span data-ttu-id="8d924-106">Geliştirme sertifikasına güvenmeyi kabul ediyorsanız **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8d924-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="8d924-107">Daha fazla bilgi için bkz. [ASP.NET Core https geliştirme sertifikasına güvenin](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="8d924-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
