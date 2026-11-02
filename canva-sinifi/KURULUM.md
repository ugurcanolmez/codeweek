# Canva Sınıfı Rezervasyon Sistemi – Kurulum

Bursa İl Millî Eğitim Müdürlüğü YEĞİTEK Birimi · BTSO Eğitim Vadisi

## Özellikler
- Hafta içi günler, 09:00-12:00 ve 13:00-17:00 arası 1 saatlik bloklar (öğle arası kapalı)
- Birden fazla **ardışık** saati tek seferde seçme
- Dolu saatler tüm cihazlarda **sayfa yenilenmeden** anında kırmızı/kilitli olur
- Dolu kartta: kurum, etkinlik, maskeli ad (Ayşe Y.), katılımcı sayısı
- Telefon / e-posta **gizli** tutulur, sadece yönetici görür
- Çakışma önleme: kayıt anında Firestore transaction ile tekrar kontrol + güvenlik kuralı ("Bu saat aralığı az önce doldu")
- Geçmiş gün ve saatler otomatik kilitli
- Yönetici: "İptal Et / Boşalt" ve Excel (CSV) indirme

## 1. Önce demo modunda deneyin
`index.html` içindeki `DEMO_MODU: true` iken Firebase gerekmez. Sayfayı açın; aynı tarayıcıda iki sekme açarsanız
birinde yaptığınız rezervasyonun diğerinde anında dolduğunu görürsünüz. Demo yönetici PIN'i: `1234`.

## 2. Firebase'i bağlayın (ücretsiz Spark planı yeterli)
1. https://console.firebase.google.com → **Proje ekle** (örn. `canva-sinifi-bursa`). Analytics gerekmez.
2. **Build → Firestore Database → Create database** → konum `europe-west3 (Frankfurt)` → *production mode*.
3. **Rules** sekmesine `firestore.rules` dosyasının tamamını yapıştırın, içindeki `yonetici@ornek.com` yerine
   kendi yönetici e-postanızı yazın → **Publish**.
4. **Build → Authentication → Get started → Email/Password**'ü etkinleştirin. **Users → Add user** ile
   yönetici e-postası + güçlü bir şifre oluşturun.
5. **Proje ayarları (⚙️) → Genel → Uygulamalarınız → Web (</>)** → uygulama ekleyin, gösterilen `firebaseConfig`
   değerlerini `index.html`'in en üstündeki **AYARLAR** bölümüne yapıştırın.
   `YONETICI_EPOSTA`'yı doldurun ve `DEMO_MODU: false` yapın.

## 3. Yayına alın
GitHub Pages açıksa adres: `https://<kullanıcı-adı>.github.io/codeweek/canva-sinifi/`
(Settings → Pages → Branch: main / root). Dosya tek başına herhangi bir web sunucusunda da çalışır.

## Ayarlanabilir değerler (index.html → AYARLAR)
| Ayar | Varsayılan | Not |
|---|---|---|
| `SINIF_KAPASITESI` | 30 | `firestore.rules` içindeki `<= 30` ile aynı olmalı |
| `SAAT_BLOKLARI` | 9,10,11,13,14,15,16 | `firestore.rules` içindeki liste ile aynı olmalı |
| `ILERI_HAFTA_SINIRI` | 12 | Kaç hafta ilerisi rezerve edilebilir |
| `DEMO_YONETICI_PIN` | 1234 | Sadece demo modunda |

## Veri yapısı
- `rezervasyonlar/{tarih_saat}` (örn. `2026-11-09_9`): herkese açık bilgiler. Belge kimliği tarih+saat olduğu
  için aynı saate ikinci bir kayıt veritabanı seviyesinde imkânsızdır.
- `iletisim/{grupId}`: ad soyad + telefon/e-posta. Sadece yönetici okuyabilir.
