# Kod Çalıştırma Açıkları

- Command Injection (OS Command Injection), uygulamanın kullanıcıdan aldığı veriyi güvenli şekilde işlemeyip bir işletim sistemi komutunun parçası olarak çalıştırması sonucu ortaya çıkan güvenlik açığıdır.

- Örneğin savunmasız bir uygulama sunucuda şuna benzer bir işlem yapıyor olsun:
- > ping -c 1 KULLANICI_GİRDİSİ
- > 127.0.0.1; ls // girilirse ve uygulama savunmasızsa, shell bunu kabaca:ping -c 1 127.0.0.1 ls çalıştırır. Böylece ls web sunucusunda çalıştırılır.

## Operatörlerin Mantığı

- > 127.0.0.1; ls // ; → İlk komutu bitirir, ardından diğer komutu çalıştırır.
- > 127.0.0.1 && ls // && → İlk komut başarılı olursa ikinci komutu çalıştırır.
- > 127.0.0.1 || ls // || → İlk komut başarısız olursa ikinci komutu çalıştırır.
- > 127.0.0.1 | ls // | → İlk komutun çıktısını ikinci komutun standart girdisine aktarır. Bu yüzden ; ile aynı anlama gelmez.

- Command Injection: Kullanıcı girdisinin işletim sistemi komutuna güvensiz biçimde eklenmesi sonucu ;, &&, ||, | gibi shell operatörlerinin kullanılarak uygulamanın amaçlamadığı ek komutların sunucuda çalıştırılabilmesi açığıdır.

## Commix

- Commix = Web uygulamalarındaki Command Injection açıklarını otomatik olarak tespit etmeye ve izinli ortamlarda doğrulamaya yarayan pentest aracıdır.
- ;, &&, | gibi operatörlerle ortaya çıkabilen Command Injection zafiyetlerinin test edilmesini otomatikleştirir.

- > commix --url="http://10.0.2.9/bWAPP/commandi.php" --cookie="security=low; PHPSESSID=f0..." --data="target=www.google.com&form=submit" // İzinli laboratuvar ortamında Burp Suite ile form gönderilirken oluşan HTTP isteği yakalanır. Yakalanan request içerisinden oturum bilgileri (Cookie) ve POST ile gönderilen form parametreleri (data) alınarak Commix’e aktarılır. Burada --url test edilecek endpoint’i, --cookie uygulamadaki mevcut oturum bilgisini, --data ise formun sunucuya gönderdiği POST parametrelerini belirtir. Commix bu bilgilerle aynı isteği yeniden oluşturarak parametrelerin Command Injection açısından savunmasız olup olmadığını test eder.
