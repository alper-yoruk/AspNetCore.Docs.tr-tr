```console
npm run release
```

Bu komut, uygulamayı çalıştırırken sunulacak istemci tarafı varlıklarını oluşturur. Varlıklar *wwwroot* klasörüne yerleştirilir.

Webpack aşağıdaki görevleri tamamladı:

* *wwwroot* dizinin içeriğini tasfiye.
* *Transpilasyon*olarak bilinen bir işlemde TypeScript'i JavaScript'e dönüştürdü.
* *Minification*olarak bilinen bir işlemde dosya boyutunu azaltmak için oluşturulan JavaScript ezilmiş.
* İşlenen JavaScript, CSS ve HTML dosyalarını *src'den* *wwwroot* dizinine kopyaladı.
* Aşağıdaki öğeleri *wwwroot/index.html* dosyasına enjekte edilmiştir:
  * Wwwroot/main'e `<link>` atıfta bulunan bir *etiket.\< hash\>.css* dosyası. Bu etiket kapanış `</head>` etiketinden hemen önce yerleştirilir.
  * Minified `<script>` *wwwroot/main'e atıfta bulunan\< bir etiket. hash\>.js* dosyası. Bu etiket kapanış `</body>` etiketinden hemen önce yerleştirilir.