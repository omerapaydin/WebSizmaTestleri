# Brute Force – Burp Suite Intruder

## Amaç

Brute Force saldırısı, bir giriş (login) mekanizmasında kullanıcı adı veya parola gibi kimlik doğrulama bilgilerinin çok sayıda farklı değer denenerek test edilmesidir.

Burp Suite içerisinde bu tür testler **Intruder** modülü kullanılarak gerçekleştirilebilir.

---

## 1. Login İsteğinin Yakalanması

1. Burp Suite üzerinde **Proxy > Intercept** aktif edilir.
2. Test ortamındaki login formundan örnek bir giriş isteği gönderilir.
3. Yakalanan HTTP isteği Intruder'a gönderilir:

`Right Click > Send to Intruder`

Örnek istek:

POST /login HTTP/1.1
Host: target.local
Content-Type: application/x-www-form-urlencoded

username=admin&password=test123

---

## 2. Positions – Hedef Alanın Belirlenmesi

**Intruder > Positions** bölümüne geçilir.

Burada Burp Suite'in değiştireceği yani payload göndereceği parametre belirlenir.

Örneğin parola alanı test edilecekse:

username=admin&password=§test123§

`§ §` işaretleri payload'ın uygulanacağı konumu gösterir.

Gerekirse **Clear §** ile otomatik seçilen alanlar temizlenir ve test edilecek parametre seçilerek **Add §** ile payload position oluşturulur.

---

## 3. Attack Type

Test senaryosuna göre uygun saldırı tipi seçilir.

### Sniper

Tek veya belirli payload konumlarını test etmek için kullanılır.

Örneğin:

username=admin
password=§PAYLOAD§

Wordlist içerisindeki değerler sırayla password parametresine gönderilir.

### Battering Ram

Aynı payload birden fazla payload position'a aynı anda uygulanır.

### Pitchfork

Birden fazla payload listesindeki değerleri paralel şekilde eşleştirerek kullanır.

Örneğin:

user1 : password1
user2 : password2
user3 : password3

### Cluster Bomb

Birden fazla payload setinin olası kombinasyonlarını test eder.

Örneğin:

username=§USER§&password=§PASS§

Kullanıcı adı ve parola listelerinin farklı kombinasyonları denenebilir.

> Çok sayıda istek oluşturabileceği için yalnızca kapsamı ve izinleri açıkça belirlenmiş test ortamlarında kullanılmalıdır.

---

## 4. Payloads – Wordlist Tanımlama

**Intruder > Payloads** bölümüne geçilir.

Payload configuration bölümünde test sırasında kullanılacak değerler belirlenir.

Örneğin parola testi için:

password123
admin123
test123
example123

Hazır bir wordlist kullanılacaksa ilgili payload listesi Burp Suite'e yüklenebilir.

Payload listesi hazırlandıktan sonra saldırı başlatılır:

`Start Attack`

---

## 5. Sonuçların Analiz Edilmesi

Intruder sonuç ekranında yalnızca HTTP status code'a bakmak yeterli değildir.

Aşağıdaki değerler birlikte incelenmelidir:

- **Status Code**
- **Length**
- **Response Body**
- **Redirect / Location Header**
- **Set-Cookie**
- **Response Time**
- Başarılı / başarısız giriş mesajları

### Length Analizi

Başarısız girişlerin response uzunlukları genellikle birbirine yakın olabilir.

Örneğin:

| Payload    | Status | Length |
| ---------- | -----: | -----: |
| test123    |    200 |   1842 |
| admin123   |    200 |   1842 |
| password   |    200 |   1842 |
| example123 |    302 |    421 |

Buradaki farklı response dikkat çekicidir ve manuel olarak incelenmelidir.

Ancak:

> **Length değerinin yüksek veya farklı olması tek başına doğru kullanıcı adı/parolanın bulunduğunu göstermez.**

Uygulamanın hata mesajları, dinamik içerikleri veya CSRF tokenları da response uzunluğunu değiştirebilir.

---

## 6. Başarılı Giriş Nasıl Doğrulanır?

Şüpheli response manuel olarak incelenmelidir.

Başarılı authentication sonucunda uygulamaya bağlı olarak:

- `200 OK` yerine `302 Found` dönmesi,
- `/dashboard`, `/profile` gibi bir sayfaya yönlendirme,
- Yeni bir session cookie oluşturulması,
- `"Invalid username or password"` mesajının kaybolması,
- `"Welcome"` / `"Logout"` gibi içeriklerin görünmesi,
- Response uzunluğunun diğerlerinden farklı olması

gibi belirtiler görülebilir.

Örneğin:

HTTP/1.1 302 Found
Location: /dashboard
Set-Cookie: session=...

Bu sonuç başarılı authentication ihtimalini güçlendirir.

---

## 7. Profesyonel Pentest Yaklaşımı

Brute force testi yalnızca "parola bulunabiliyor mu?" şeklinde değerlendirilmemelidir.

Aynı zamanda uygulamanın brute-force saldırılarına karşı sahip olduğu güvenlik mekanizmaları test edilir:

- Rate Limiting
- Account Lockout
- CAPTCHA
- MFA / 2FA
- IP tabanlı kısıtlama
- Başarısız girişlerin loglanması
- Kullanıcıya güvenlik bildirimi gönderilmesi
- Username Enumeration engellemesi

Örneğin uygulama yüzlerce başarısız giriş denemesine herhangi bir sınırlama uygulamıyorsa bu durum ayrı bir güvenlik bulgusu olabilir.

---

## Kısa Akış

Login Request
↓
Proxy ile isteği yakala
↓
Send to Intruder
↓
Positions
↓
Test edilecek parametreyi seç
↓
Attack Type belirle
↓
Payloads / Wordlist ekle
↓
Start Attack
↓
Response'ları karşılaştır
↓
Status + Length + Body + Redirect + Cookie
↓
Şüpheli sonucu manuel doğrula
↓
Brute Force korumalarını değerlendir

---

## Önemli Not

`Length farklı/yüksek → parola kesin doğru`

şeklinde değerlendirme yapılmamalıdır.

Daha doğru yaklaşım:

`Anormal Response → İncele → Karşılaştır → Manuel Doğrula`

Profesyonel bir sızma testinde amaç yalnızca çalışan bir credential bulmak değil, **authentication mekanizmasının brute-force saldırılarına karşı ne kadar dayanıklı olduğunu değerlendirmektir.**

### Kısaca

- Login işlemi sırasında HTTP isteği **Burp Suite** ile yakalanır ve **Intruder** modülüne gönderilir.

- **Positions:** `Add` seçeneği kullanılarak test edilecek `username` veya `password` parametresi **payload position** olarak belirlenir. Test senaryosuna göre **Sniper, Battering Ram, Pitchfork veya Cluster Bomb** saldırı tiplerinden uygun olanı seçilir.

- **Payloads:** `Payload Options` bölümüne kullanılacak **wordlist** eklenir. Gerekli yapılandırmalar tamamlandıktan sonra `Start Attack` ile test başlatılır.

- **Sonuç Analizi:** Intruder sonuçlarında `Status Code`, `Length`, yönlendirme ve response içeriği karşılaştırılır. Diğer yanıtlardan belirgin şekilde farklı `Length` değerine sahip response'lar olası başarılı giriş açısından incelenir.

> **Not:** `Length` değerinin yüksek veya farklı olması tek başına doğru kullanıcı adı veya parolanın bulunduğunu göstermez. Şüpheli response; içerik, HTTP durum kodu, yönlendirme ve session/cookie değişiklikleriyle birlikte doğrulanmalıdır.
