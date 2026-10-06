# Directory Traversal

- Directory Traversal (Path Traversal), web uygulamasındaki dosya yolu parametrelerinin yeterince kontrol edilmemesi sonucu kullanıcının izin verilen dizinin dışındaki dosyalara erişebilmesine yol açan güvenlik açığıdır. Çoğu zaman URL’deki dosya yolu alanında/parametresinde test edilir. Ama ../ ifadesini URL’nin rastgele bir yerine değil, uygulamanın dosya adı veya dosya yolu aldığı parametreye verirsin.

- > ../../../../etc/passwd
- > /file?name=rapor.txt // şeklinde dosya okuyorsa ve savunmasızsa, manipüle edilen dosya yolu uygulamanın amaçladığı klasörün dışındaki sistem dosyalarına erişmeye çalışabilir.

- Dosya parametresi → ../ ile dizin dışına çıkma → Yetkisiz dosyaya erişim

- Korunma: Kullanıcıdan doğrudan dosya yolu almamak, allowlist kullanmak, yolları normalize/doğrulamak ve uygulamayı minimum dosya sistemi yetkileriyle çalıştırmak.

Örneğin izinli lab uygulamasında normal URL:

- > http://test.local/view.php?file=rapor.txt // Burada file parametresi dosya seçiyor. Directory Traversal testi bu parametre üzerinde yapılır:
- > http://test.local/view.php?file=../../../../etc/passwd

  file=rapor.txt
  ↓
  file=../../../../etc/passwd
  ↓
  Üst dizinlere çıkmayı dene
  ↓
  Uygulama izin veriyorsa dosya okunabilir

## Dotdotpwn

- DotDotPwn, web uygulamaları ve bazı ağ servislerinde Directory/Path Traversal açıklarını otomatik olarak test etmek için kullanılan bir pentest aracıdır.

- > dotdotpwn -m http -h "10.0.2.9/bWAPP/direc....?page=message.txt" -S // İzinli laboratuvar ortamında belirtilen HTTP hedefini Directory Traversal açısından otomatik olarak test eder.

-m http → HTTP modunu kullanır.
-h → Test edilecek hedefi belirtir.
-S → HTTPS/SSL bağlantısı kullanılacağını belirtir.

- Eğer hedefin http:// ise -S kullanmamalısın; -S SSL/HTTPS içindir.
