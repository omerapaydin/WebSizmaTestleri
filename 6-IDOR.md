# IDOR Açığı

## IDOR Nedir?

**IDOR (Insecure Direct Object Reference)**, uygulamanın bir kullanıcının belirli bir kaynağa erişme yetkisini sunucu tarafında yeterince kontrol etmemesi sonucu ortaya çıkan **Broken Access Control** açığıdır.

Kullanıcı; URL, form veya API isteğindeki `id`, `userId`, `orderId` gibi bir değeri değiştirerek başka bir kullanıcıya ait veriye erişebiliyorsa IDOR açığı bulunabilir.

---

## Basit Örnek

Kendi hesabımızdaki sipariş sayfası:

```text
/account/order?id=125
```

Burada:

```text
id=125
```

bizim siparişimizin ID'sidir.

URL'deki ID değiştirilir:

```text
/account/order?id=126
```

Eğer `126` başka bir kullanıcıya ait olmasına rağmen sipariş bilgileri görüntülenebiliyorsa **IDOR açığı vardır.**

### Mantık

```text
Kullanıcı A → /order?id=125 → Kendi siparişi ✓

Kullanıcı A → /order?id=126 → Başkasının siparişi ✗
                              ↓
                     Sunucu izin veriyorsa
                              ↓
                         IDOR Açığı
```

---

## Profil Örneği

Normal istek:

```text
/profile?id=50
```

ID değiştirilir:

```text
/profile?id=51
```

Eğer oturumdaki kullanıcının görmemesi gereken `51` numaralı kullanıcının özel profil bilgileri görüntülenebiliyorsa IDOR olabilir.

---

## API Örneği

```http
GET /api/users/125/orders
```

İstek:

```http
GET /api/users/126/orders
```

olarak değiştirildiğinde başka kullanıcının siparişleri döndürülüyorsa API'de **yetkilendirme kontrolü eksiktir.**

---

## Burp Suite ile Test Mantığı

İzinli laboratuvar ortamında:

```text
İki test hesabı oluştur
        ↓
A hesabıyla işlem yap
        ↓
Burp Suite ile request'i yakala
        ↓
id / userId / orderId değerini
B hesabındaki nesnenin ID'siyle değiştir
        ↓
Request'i gönder
        ↓
Sunucu erişimi engelliyor mu kontrol et
```

Sunucunun ideal olarak yetkisiz isteğe `403 Forbidden` gibi uygun bir cevap vermesi veya kaynağı kullanıcıya göstermemesi gerekir.

---

## Önemli Nokta

ID'nin tahmin edilebilir olması **tek başına IDOR değildir.**

Örneğin:

```text
?id=100
?id=101
?id=102
```

değerlerinin tahmin edilebilmesi tek başına güvenlik açığı oluşturmaz.

Asıl açık:

> **Kullanıcının yetkisi olmadığı bir nesneye yalnızca referansı/ID'yi değiştirerek erişebilmesidir.**

---

## Güvenlik Önlemleri

- Her istekte sunucu tarafında **authorization (yetkilendirme) kontrolü** yapılmalıdır.
- Kaynağın oturumdaki kullanıcıya ait olup olmadığı doğrulanmalıdır.
- Sadece arayüzde butonları veya bağlantıları gizlemek güvenlik sağlamaz.
- UUID gibi tahmin edilmesi zor ID'ler ek koruma sağlayabilir ancak **yetkilendirme kontrolünün yerine geçmez.**

## Kısaca

**IDOR = ID'yi değiştirdim ve sunucu yetkimi kontrol etmeden başka kullanıcıya ait veriye/işleme erişmeme izin verdi.**
