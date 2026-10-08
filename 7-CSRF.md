# CSRF — Şifre Değiştirme Örneği

## Senaryo

Bir web sitesinde giriş yapmış kullanıcı şifresini aşağıdaki istekle değiştirebiliyor olsun:

```http
GET /change-password?newPassword=YeniSifre123
```

ve uygulama bu işlemde:

- Mevcut şifreyi tekrar istemiyor.
- CSRF Token kontrol etmiyor.
- İsteğin gerçekten kullanıcı tarafından başlatıldığını doğrulamıyor.

Bu durumda CSRF riski oluşabilir.

---

## Normal İşlem

Kullanıcı siteye giriş yapar:

```text
Kullanıcı
   ↓
Siteye giriş yapar
   ↓
Session Cookie oluşur
   ↓
Şifre değiştirme isteği gönderir
   ↓
Sunucu şifreyi değiştirir
```

Örneğin:

```text
https://lab.test/change-password?newPassword=YeniSifre123
```

> Gerçek uygulamalarda parolanın URL/GET parametresinde gönderilmesi ayrıca kötü bir tasarımdır. Buradaki örnek yalnızca CSRF mantığını göstermek içindir.

---

# CSRF Senaryosu

Saldırgan kendi izinli test hesabında şifre değiştirme işlemini inceler ve işlemin şu URL ile yapılabildiğini fark eder:

```text
https://lab.test/change-password?newPassword=Test123456
```

Saldırgan bu bağlantıyı kurbana gönderir.

```text
Saldırgan
   ↓
Hazırlanmış bağlantı
   ↓
Kurban
```

Kurban aynı tarayıcıda `lab.test` sitesine zaten giriş yapmış durumdaysa bağlantıyı açar.

Tarayıcı hedef siteye isteği gönderir:

```http
GET /change-password?newPassword=Test123456
Cookie: session=KURBANIN_OTURUMU
```

Buradaki önemli nokta:

**Saldırgan kurbanın session cookie'sini bilmek zorunda değildir.**

Uygun cookie koşullarında tarayıcı, hedef siteye ait mevcut oturum bilgisini isteğe kendisi ekleyebilir.

Sunucu yalnızca:

```text
Geçerli bir session var mı?
```

diye kontrol edip:

```text
Bu isteği kullanıcı gerçekten başlattı mı?
CSRF Token geçerli mi?
Mevcut parola doğrulandı mı?
```

gibi ek kontrolleri yapmıyorsa işlem gerçekleşebilir.

---

## Akış

```text
Kurban hedef siteye giriş yapmış
             ↓
Session tarayıcıda mevcut
             ↓
Hazırlanmış bağlantıyı açar
             ↓
Tarayıcı hedef siteye istek gönderir
             ↓
Mevcut oturum bilgisi uygun koşullarda
isteğe otomatik eklenir
             ↓
Sunucuda CSRF koruması yok
             ↓
İstek kurbanın oturumu altında işlenir
             ↓
Şifre değişikliği gerçekleşebilir
```

---

# Neden CSRF?

Çünkü problem:

```text
Saldırgan kurbanın şifresini biliyor
```

değildir.

Problem:

```text
Kurban zaten giriş yapmış
        +
Tarayıcı mevcut oturumu kullanıyor
        +
Sunucu isteğin kaynağını yeterince doğrulamıyor
        =
CSRF
```

---

# Önemli Ayrım

Sadece:

```text
Linki kurbana gönderdim
```

demek CSRF için yeterli değildir.

Başarılı olabilmesi uygulamanın tasarımına ve tarayıcı/cookie politikalarına bağlıdır.

Örneğin modern `SameSite` cookie ayarları bazı cross-site isteklerde cookie gönderilmesini engelleyebilir.

---

# Nasıl Önlenir?

Şifre değiştirme gibi hassas işlemlerde:

- CSRF Token kullanılmalı.
- İşlem `GET` ile yapılmamalı.
- Uygun `SameSite` cookie politikası kullanılmalı.
- `Origin` / `Referer` doğrulaması ek savunma olarak kullanılabilir.
- Mevcut parola yeniden istenebilir.
- Kritik hesap işlemlerinde yeniden kimlik doğrulama uygulanabilir.

---

## Kısaca

**CSRF = Kullanıcının açık oturumundan yararlanılarak, onun istemediği bir işlemin onun tarayıcısı üzerinden gerçekleştirilmesidir.**

Şifre değiştirme örneğinde:

```text
Kurban giriş yapmış
→ hazırlanmış isteği tetikler
→ tarayıcı mevcut oturumu kullanır
→ CSRF koruması yoksa
→ şifre değişikliği kurbanın hesabında gerçekleşebilir
```

# CSRF (Cross-Site Request Forgery)

## CSRF Nedir?

**CSRF**, giriş yapmış bir kullanıcının mevcut oturumundan yararlanılarak, kullanıcının istemediği bir işlemin onun adına gerçekleştirilmesine neden olabilen güvenlik açığıdır.

Temel mantık:

```text
Kurban siteye giriş yapmış
        ↓
Session / Cookie mevcut
        ↓
Kurban istenmeyen bir isteği tetikler
        ↓
Tarayıcı uygun koşullarda oturum bilgisini gönderir
        ↓
Sunucu CSRF kontrolü yapmaz
        ↓
İşlem kurbanın hesabında gerçekleşebilir
```

> CSRF sadece şifre değiştirme değildir. Kullanıcının hesabında veya sistemde değişiklik yapan birçok işlem CSRF'den etkilenebilir.

---

# 1 — Şifre Değiştirme

Örneğin uygulama:

```http
POST /account/change-password

newPassword=Test123
```

şeklinde çalışıyor olsun.

Kullanıcı giriş yapmış durumdayken bu istek onun tarayıcısından tetiklenirse ve sunucu CSRF doğrulaması yapmıyorsa:

```text
Kurban giriş yapmış
      ↓
Şifre değiştirme isteği tetiklenir
      ↓
Session kullanılır
      ↓
CSRF kontrolü yok
      ↓
Şifre değişebilir
```

---

# 2 — E-posta Değiştirme

Örneğin:

```http
POST /account/change-email

email=test@example.com
```

Sunucu yalnızca kullanıcının giriş yapmış olmasını kontrol ediyor ve CSRF koruması uygulamıyorsa kullanıcının e-posta adresinin istemediği şekilde değiştirilmesi riski oluşabilir.

```text
Kurbanın Session'ı
       ↓
E-posta değiştirme isteği
       ↓
CSRF kontrolü yok
       ↓
E-posta değişebilir
```

Bu özellikle önemlidir çünkü e-posta adresi bazı uygulamalarda hesap kurtarma işlemlerinde kullanılır.

---

# 3 — Telefon Numarası Değiştirme

Örneğin:

```http
POST /account/change-phone

phone=...
```

CSRF koruması bulunmuyorsa kullanıcının hesabındaki telefon numarasının değiştirilmesi gibi bir risk oluşabilir.

---

# 4 — Para Transferi

Örnek bir laboratuvar uygulaması:

```http
POST /transfer

to=test-user
amount=100
```

Sunucu:

```text
Kullanıcı giriş yapmış mı? → EVET
CSRF doğrulaması var mı?   → HAYIR
```

şeklinde davranıyorsa kullanıcının istemediği bir finansal işlemin gerçekleştirilmesi riski oluşabilir.

---

# 5 — Teslimat / Fatura Adresi Değiştirme

E-ticaret uygulamasında:

```http
POST /account/address

address=YeniAdres
```

CSRF koruması bulunmuyorsa kayıtlı adres kullanıcının bilgisi dışında değiştirilebilir.

```text
Mevcut adres
     ↓
İstenmeyen request
     ↓
CSRF kontrolü yok
     ↓
Adres değişebilir
```

---

# 6 — Sipariş / İşlem Oluşturma

Örneğin:

```http
POST /order/create

productId=25
quantity=1
```

Uygulamanın tasarımına bağlı olarak CSRF korumasının bulunmaması kullanıcının istemediği bir sipariş veya işlemin oluşturulmasına neden olabilir.

---

# 7 — Hesap Silme

Örneğin:

```http
POST /account/delete
```

Sunucu yalnızca mevcut session'a güveniyor ve ek doğrulama yapmıyorsa:

```text
Kurban giriş yapmış
      ↓
Hesap silme isteği tetiklenir
      ↓
CSRF kontrolü yok
      ↓
Hesap silinebilir
```

Bu nedenle hesap silme gibi kritik işlemlerde ek doğrulamalar önemlidir.

---

# 8 — Profil Bilgilerini Değiştirme

CSRF ile hedeflenebilecek işlemler arasında:

```text
Ad / Soyad
Kullanıcı adı
Telefon
Adres
Profil bilgileri
Hesap tercihleri
```

gibi alanların değiştirilmesi de bulunabilir.

Örneğin:

```http
POST /profile/update

username=newname
```

---

# 9 — Abonelik İşlemleri

Bir uygulamada:

```text
Abonelik başlatma
Abonelik iptal etme
Bildirim ayarlarını değiştirme
Hizmet etkinleştirme
```

gibi işlemler de CSRF açısından incelenebilir.

---

# 10 — Yönetici (Admin) İşlemleri

CSRF yalnızca normal kullanıcı hesaplarını etkilemez.

Bir yönetici oturumu hedef olduğunda sonuç daha ciddi olabilir.

Örneğin CSRF'ye karşı korunmayan:

```text
Kullanıcı ayarlarını değiştirme
Yetki değiştirme
Sistem ayarlarını değiştirme
İçerik silme
İçerik yayınlama
```

gibi yönetim işlemleri bulunabilir.

Burada işlem:

```text
Admin giriş yapmış
       ↓
Geçerli admin session'ı mevcut
       ↓
İstenmeyen request tetiklenir
       ↓
CSRF kontrolü yok
       ↓
İşlem admin yetkileriyle gerçekleştirilebilir
```

---

# CSRF'de Neye Bakılır?

Pentest sırasında özellikle **sunucunun durumunu değiştiren işlemler** önemlidir.

Örneğin:

```text
Şifre değiştirme
E-posta değiştirme
Telefon değiştirme
Profil güncelleme
Adres değiştirme
Para transferi
Sipariş oluşturma
Hesap silme
Abonelik değiştirme
Admin işlemleri
```

---

# CSRF Koruması

Temel güvenlik önlemleri:

- **CSRF / Anti-Forgery Token**
- Uygun **SameSite Cookie** politikası
- Hassas işlemleri `GET` ile gerçekleştirmemek
- `Origin` / `Referer` doğrulaması
- Kritik işlemlerde mevcut parolayı yeniden istemek
- Gerektiğinde yeniden kimlik doğrulama
- Framework'lerin yerleşik CSRF korumalarını kullanmak

Örneğin:

```html
<input type="hidden" name="csrf_token" value="RANDOM_TOKEN" />
```

Sunucu:

```text
Request
   ↓
Session geçerli mi?
   ↓
CSRF Token geçerli mi?
   ↓
EVET → İşlemi değerlendir
HAYIR → İsteği reddet
```

---

# Kısaca

**CSRF = Kullanıcının açık oturumundan yararlanarak onun adına istemediği bir işlemin gerçekleştirilmesine neden olabilen güvenlik açığıdır.**

```text
Şifre değiştirme
E-posta değiştirme
Telefon değiştirme
Para transferi
Adres değiştirme
Sipariş oluşturma
Profil değiştirme
Hesap silme
Abonelik işlemleri
Admin işlemleri
```

gibi **state-changing (durum değiştiren) işlemler** CSRF açısından önemlidir.

> CSRF'de saldırganın kurbanın Session Cookie'sini bilmesi veya çalması gerekmez. Temel problem, kurbanın tarayıcısındaki mevcut oturumun istenmeyen bir işlem için kullanılabilmesidir.

> POST parametreleri adres çubuğunda görünmez; Burp Suite ile istek yakalanarak URL, Cookie ve request body içerisindeki parametreler incelenebilir ve CSRF kontrolleri test edilebilir.

Burp Suite → Proxy → HTTP History
↓
Şifre değiştirme işlemi
↓
Request'i bul
↓
Send to Repeater
↓
CSRF Token var mı incele
↓
Token olmadan istek kabul ediliyor mu?
↓
Cookie / SameSite / Origin kontrollerini incele
↓
CSRF riskini değerlendir
