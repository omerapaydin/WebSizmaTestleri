# Html Enjeksiyonları ve Daha Fazlası

## HTML Enjeksiyonu Açığı

- HTML Injection, kullanıcıdan alınan verinin güvenli biçimde encode/sanitize edilmeden HTML sayfasına eklenmesi sonucu, kullanıcının sayfanın HTML yapısını değiştirebilmesi açığıdır.

- Form kısmına örnek <b>Kalın Yazı</b> yazıldığında yazı kalın görünüyorsa HTML yorumlanıyor olabilir.

## Stored HTML Enjeksiyonu Açığı

- Stored HTML Injection, kullanıcı tarafından girilen HTML kodunun veritabanına kaydedilip, daha sonra sayfayı görüntüleyen kullanıcılara HTML olarak gösterilmesi durumudur.
  Form → Veritabanına kayıt → Sayfa tekrar açılır → HTML çalıştırılır/görüntülenir

## Netcat

- Netcat = TCP/UDP üzerinden bağlantı kurmaya ve dinlemeye yarayan ağ aracıdır.

- Form kısmına <iframe src="http://10.0.2.4:4545/test" height="0" width="0"></iframe> // kendi ip adresi girilir
- > nc -nvlp 4545 // localde dinleme başlar. İsteğin geldiği kaynak IP gözlemlenebilir

### Güvenlik Önlemleri

- Kullanıcı girdileri uygun şekilde output encode edilmelidir.
- HTML kabul edilmesi gerekiyorsa güvenilir bir HTML sanitizer kullanılmalıdır.
- Gereksiz HTML etiketleri ve özellikleri kabul edilmemelidir.
- CSP ek bir savunma katmanı olarak kullanılabilir.
