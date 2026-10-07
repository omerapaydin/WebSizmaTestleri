# XSS

- XSS (Cross-Site Scripting), web sitelerinde görülen bir güvenlik açığıdır. Bu açık sayesinde saldırgan, siteye zararlı JavaScript kodu ekleyebilir ve siteyi kullanan kişilerin tarayıcısında çalıştırabilir.

## XSS Türleri

| Reflected XSS | Payload request'ten response'a yansır |

| Stored XSS | Payload saklanır ve daha sonra görüntülendiğinde çalışır |

| DOM-Based XSS | İstemci tarafı JavaScript'in DOM'u güvensiz işlemesi sonucu oluşur |

- Stored XSS sadece şu durumda olur:

✔ Kullanıcı girdisi DB’ye kaydediliyor
✔ Sonra tekrar HTML olarak ekrana basılıyor

- Stored XSS, kullanıcı verisinin veritabanına kaydedilip diğer kullanıcılara tekrar gösterildiği tüm form alanlarında ortaya çıkabilir.

- Forma girilen bilgileri veritabanına kaydeder. Tarayıcı yenilense bile girilen form ekranda gösterildiği için zararlı js kodu eklenirse siteye giren tüm kullanıcılar hacklenir

- Güvenlik önlemi :
  • Kullanıcı girdisi HTML encode edilmelidir
  • Output encoding uygulanmalıdır
  • Whitelist input validation kullanılmalıdır
  • JavaScript içeriği filtrelenmemeli, hiç çalıştırılmamalıdır
  • CSP (Content Security Policy) kullanılmalıdır

- Forma <script>alert("i hack you")</script> kaydedildiğinde siteye her giren bu alertle karşılaşır.

En temel doğrulama:

```html
<script>
  alert(1);
</script>
```

Diğer yaygın test biçimleri:

```html

<ScRiPt>alert(1)</ScRiPt>

</p><script>alert(1)</script><p>

<img src=x onerror=alert(1)>

<svg onload=alert(1)></svg>

<input autofocus onfocus=alert(1)>

<button onclick=alert(1)>Test</button>

- Stored XSS nasıl anlaşılır
  • Yorum yaz
  • Script ekle
  • Sayfayı yenile
  • Başkası girsin
  • Script çalışıyorsa → Stored XSS

* Beef kullanarak

- Beef'in verdiği hook kodunu tarayıcıda form kısmına gir ve kaydet. Kullanıcılar o sayfaya girdiğinde beef arayüzünde online olurlar

# Savunma

XSS'e karşı temel savunmalar:

- Context'e uygun output encoding

- Kullanıcı girdisini gerektiğinde sanitize etmek

- Güvensiz `innerHTML` kullanımından kaçınmak

- Güvenli DOM API'leri kullanmak

- Gereksiz inline JavaScript/event handler kullanımından kaçınmak

- Content Security Policy (CSP) kullanmak

- Framework'lerin otomatik escaping mekanizmalarını devre dışı bırakmamak
```
