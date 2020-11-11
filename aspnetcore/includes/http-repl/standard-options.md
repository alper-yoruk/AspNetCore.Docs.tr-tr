* `-F|--no-formatting`

  Durumu HTTP yanıt biçimlendirmesini belirten bir bayrak.

* `-h|--header`

  HTTP istek üst bilgisini ayarlar. Aşağıdaki iki değer biçimi desteklenir:

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  HTTP yanıt gövdesinin yazılması gereken bir dosyayı belirtir. Örneğin, `--response:body "C:\response.json"`. Dosya yoksa oluşturulur.

* `--response:headers`

  HTTP yanıt üst bilgilerinin yazılması gereken bir dosyayı belirtir. Örneğin, `--response:headers "C:\response.txt"`. Dosya yoksa oluşturulur.

* `-s|--streaming`

  Mevcut bir bayrak, HTTP yanıtının akışını mümkün bir şekilde sunar.
