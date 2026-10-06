# Directory Traversal

- Directory Traversal (Path Traversal), web uygulamasındaki dosya yolu parametrelerinin yeterince kontrol edilmemesi sonucu kullanıcının izin verilen dizinin dışındaki dosyalara erişebilmesine yol açan güvenlik açığıdır. Çoğu zaman URL’deki dosya yolu alanında/parametresinde test edilir. Ama ../ ifadesini URL’nin rastgele bir yerine değil, uygulamanın dosya adı veya dosya yolu aldığı parametreye verirsin.

- > ../../../../etc/passwd
- > /file?name=rapor.txt // şeklinde dosya okuyorsa ve savunmasızsa, manipüle edilen dosya yolu uygulamanın amaçladığı klasörün dışındaki sistem dosyalarına erişmeye çalışabilir.

- Dosya parametresi → ../ ile dizin dışına çıkma → Yetkisiz dosyaya erişim

- Korunma: Kullanıcıdan doğrudan dosya yolu almamak, allowlist kullanmak, yolları normalize/doğrulamak ve uygulamayı minimum dosya sistemi yetkileriyle çalıştırmak.
