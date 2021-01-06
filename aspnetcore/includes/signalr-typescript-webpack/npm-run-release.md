```console
npm run release
```

Bu komut, uygulamayı çalıştırırken sunulacak istemci tarafı varlıkları oluşturur. Varlıklar *Wwwroot* klasörüne yerleştirilir.

WebPack aşağıdaki görevleri tamamladı:

* *Wwwroot* dizininin içeriği temizlendi.
* TypeScript 'i *transpilation* olarak bilinen bir işlemde JavaScript 'e dönüştürüyordu.
* Dosya boyutunu *minbirleşme* olarak bilinen bir işlemde azaltmak Için üretilen JavaScript 'i karıştırın.
* İşlenen JavaScript, CSS ve HTML dosyaları *src* 'den *Wwwroot* dizinine kopyalanamadı.
* *Wwwroot/index.html* dosyasına aşağıdaki öğeler Eklendi:
  * `<link>`Wwwroot/Main 'e başvuran bir etiket *. \<hash\> CSS* dosyası. Bu etiket, kapanış etiketinden hemen öncesine yerleştirilir `</head>` .
  * `<script>`Mini olarak belirtilen *Wwwroot/Main öğesine başvuran bir etiket. \<hash\> JS* dosyası. Bu etiket, kapanış etiketinden hemen öncesine yerleştirilir `</body>` .