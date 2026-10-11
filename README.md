<div align="center">

  <img src="assets/logo.png" alt="FloraScan Logo" width="130" style="border-radius: 26px;" />

  # FloraScan: AI-Powered Precision Agriculture
  ### Akıllı Tarım, Bilgisayarlı Görü ve Hassas Hedefleme Sistemi

  <p align="center">
    <strong>Ekin ve yabani otları mikrosaniyeler içinde teşhis eden, robotik ilaçlama nozulları için milimetrik hedef koordinatı üreten yeni nesil tarımsal yapay zeka ekosistemi.</strong>
  </p>

  <p align="center">
    <a href="#"><img src="https://img.shields.io/badge/T%C3%9CB%C4%B0TAK-2209--A%20Destekli-blue?style=for-the-badge&logo=shield" alt="TUBITAK 2209-A" /></a>
    <a href="#"><img src="https://img.shields.io/badge/Akademik-Bitirme%20Tezi-success?style=for-the-badge&logo=googlescholar" alt="Bitirme Projesi" /></a>
    <a href="#"><img src="https://img.shields.io/badge/Model%20Ba%C5%9Far%C4%B1s%C4%B1-%2597.00%20Accuracy-darkgreen?style=for-the-badge&logo=scikitlearn" alt="Model Doğruluğu" /></a>
    <a href="#"><img src="https://img.shields.io/badge/Cross%20Validation-%2596.8-brightgreen?style=for-the-badge" alt="Cross Validation" /></a>
  </p>

  <p align="center">
    <img src="https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter&logoColor=white" />
    <img src="https://img.shields.io/badge/FastAPI-Clean%20Arch-009688?logo=fastapi&logoColor=white" />
    <img src="https://img.shields.io/badge/Supabase-PostgreSQL%2015-3ECF8E?logo=supabase&logoColor=white" />
    <img src="https://img.shields.io/badge/Render-Cloud%20PaaS-46E3B7?logo=render&logoColor=white" />
    <img src="https://img.shields.io/badge/Computer%20Vision-OpenCV%20%7C%20GLCM%20%7C%20HOG-black?logo=opencv" />
  </p>

</div>

---

> 🔒 **Gizlilik ve Fikri Mülkiyet Bildirimi (Intellectual Property Notice):**  
> Bu proje; **TÜBİTAK 2209-A Üniversite Öğrencileri Araştırma Projeleri Destekleme Programı** ve **Mühendislik Lisans Bitirme Tezi** kapsamında geliştirilmektedir. Akademik yayın, patent ve tez teslim süreçlerinin korunması amacıyla projenin tam kaynak kodları (Core Source Code) bu vitrin deposunda açık kaynak olarak sunulmamaktadır. Bu repository; projenin mimarisini, mühendislik yaklaşımlarını, model başarım metriklerini ve arayüzünü sergileyen resmi vitrin deposudur.

---

## 🌾 Projeye Genel Bakış (Overview)

Geleneksel tarımsal mücadelede yabani otlar tüm tarlaya homojen ve aşırı miktarda kimyasal herbisit (ilaç) püskürtülerek yok edilmeye çalışılmaktadır. Bu durum; toprak kirliliğine, yeraltı sularının zehirlenmesine, ekinlerin zarar görmesine ve astronomik kimyasal maliyetlerine yol açmaktadır.

**FloraScan**, akıllı telefon kamerasını kullanarak tarladaki bitkiyi saniyeler içinde analiz eder:
1. **Teşhis:** Bitkinin **Kültür Bitkisi (Crop)** mi yoksa **Yabani Ot (Weed)** mu olduğunu %97 doğrulukla sınıflandırır.
2. **Morfolojik Odaklanma:** Ana bitki gövdesine odaklanan dinamik bir **Sınırlayıcı Kutu (Bounding Box)** hesaplar.
3. **Hassas Hedefleme (Crosshair):** Eğer tespit edilen bitki yabani ot ise, bitkinin geometrik ağırlık merkezini çıkararak pülverizatör veya lazer nozulunun kilitlenebileceği **Piksel ve Normalize Hedef Koordinatlarını ($X, Y, nX, nY$)** üretir.
4. **Ekin Koruma Modu:** Eğer bitki bir kültür bitkisi ise, ilaçlama tamamen bloke edilir (`target_point = null`) ve ekin koruma kalkanı devreye girer.

---

## 📱 Mobil Arayüz ve Kullanıcı Deneyimi (UI Showcase)

Tarlanın sert güneş ışığı ve zorlu çevre koşulları dikkate alınarak yüksek kontrastlı **Agro-Visibility System** arayüzü tasarlanmıştır:

<div align="center">
  <table>
    <tr>
      <td align="center"><strong>Fotoğraf Seçimi ve Lazerli Tarama Akışı</strong></td>
      <td align="center"><strong>Analiz Sonucu, Nişangah ve Koordinat Kartı</strong></td>
    </tr>
    <tr>
      <td align="center"><img src="assets/screenshot_01.png" width="340" style="border-radius: 14px; box-shadow: 0 4px 14px rgba(0,0,0,0.15);" /></td>
      <td align="center"><img src="assets/screenshot_02.png" width="340" style="border-radius: 14px; box-shadow: 0 4px 14px rgba(0,0,0,0.15);" /></td>
    </tr>
  </table>
</div>

* **Yeşil Lazer Tarama:** Görsel seçildiği anda yukarıdan aşağıya salınan reaktif lazer çizgisi.
* **Görsel Optimizasyonu:** Çekilen 10+ MB ham kamera görseli, arka planda $1024 \times 1024$ JPEG %85 formatına sıkıştırılarak hücresel veri tüketimi %95 oranında düşürülür.
* **Sonuç Ekranı:** Neon yeşil parlayan sınırlayıcı kutu, dinamik hedefleme artısı, güven skoru rozeti ve robotik entegrasyon koordinatları.

---

## 🧠 Bilimsel ve Mühendislik Yaklaşımı

### Neden Derin Öğrenme Değil de 9 Boyutlu Hibrit Bilgisayarlı Görü?
Ağır derin öğrenme modelleri (ResNet, VGG, YOLO) yüzlerce megabayt bellek gerektirir, mobil cihazlarda aşırı ısınmaya ve pil tüketimine neden olur. 

FloraScan, yaprak morfolojisini doğrudan özetleyen **9 boyutlu hibrit bir matematiksel öznitelik vektörü** geliştirmiştir:

$$\vec{V} = [H_{mean}, S_{mean}, V_{mean}, Green_{ratio}, Contours, Area, Contrast_{GLCM}, Homogeneity_{GLCM}, HOG_{mean}]$$

```mermaid
flowchart LR
    IMG["Girdi Görüntüsü<br/>(128x128 Piksel)"] --> PRE["Renk ve Uzay Dönüşümü<br/>(HSV + Gri Seviye)"]
    
    PRE --> F1["1-3. HSV Renk Ortalamaları<br/>[H_mean, S_mean, V_mean]"]
    PRE --> F2["4. Yeşil Maske Alan Oranı<br/>[Green Ratio]"]
    PRE --> F3["5-6. Morfolojik Kontur ve Alan<br/>[Contours, Area]"]
    PRE --> F4["7-8. GLCM Doku Analizi<br/>[Contrast, Homogeneity]"]
    PRE --> F5["9. HOG Gradyan Kenar Histogramı<br/>[HOG Mean]"]
    
    F1 & F2 & F3 & F4 & F5 --> VEC["9 BOYUTLU HİBRİT VEKTÖR"]
    VEC --> MODEL["Random Forest Sınıflandırıcı<br/>(n_estimators=200, max_depth=10)"]
    MODEL --> OUT["Sınıf: Crop / Weed / Unknown<br/>Güven Skoru: %0 - %100"]
```

### Model Karşılaştırma ve Başarım Tablosu

| Algoritma | Parametreler | Test Doğruluğu | Değerlendirme |
| :--- | :--- | :--- | :--- |
| **Random Forest (Seçilen)** | `n_estimators=200, max_depth=10` | **%97.00** | **En yüksek genelleme, mikrosaniyeler mertebesinde çıkarım, aşırı öğrenmeye (overfitting) karşı dirençli.** |
| **Gradient Boosting (GBDT)** | `n_estimators=100, lr=0.1` | %95.20 | Yüksek doğruluk fakat daha uzun eğitim ve çıkarım süresi. |
| **Destek Vektör Makineleri (SVM)**| `RBF Kernel, C=1.0` | %94.80 | Kararlı karar sınırları, yüksek doğruluk. |
| **Lojistik Regresyon** | `max_iter=1000` | %88.40 | Doğrusal sınır; karmaşık yaprak ışık varyasyonlarında yetersiz. |

* **5-Kattı Çapraz Doğrulama (5-Fold Cross Validation):** **%96.8** kararlı doğruluk elde edilmiş, modelin ezber yapmadığı ispatlanmıştır.
* **Model Dosya Boyutu:** Yalnızca **1.7 MB** (`flora_model.joblib`).

---

## 🏛️ Uçtan Uca Bulut ve Sistem Mimarisi

Sistem; mobil donanım, bulut API iletişimi, görüntü işleme mikroservisi ve kurumsal ilişkisel veritabanı içeren modern bir dağıtık mimari üzerinde çalışır:

```mermaid
sequenceDiagram
    autonumber
    actor U as Çiftçi / Araştırmacı
    participant APP as Flutter Mobil İstemci
    participant API as FastAPI Cloud API (Render EU)
    participant CV as CV Engine (OpenCV Headless & Skimage)
    participant ML as ML Inference (Random Forest)
    participant DB as Supabase PostgreSQL (RLS Protected)

    U->>APP: Kamera ile Çek / Galeriden Yükle
    Note over APP: Arka Planda Görsel Sıkıştırma (1024x1024)
    APP->>API: POST /predict (Multipart/form-data via HTTPS)
    API->>CV: extract_features_from_array()
    CV-->>API: 9 Boyutlu Öznitelik Vektörü
    API->>ML: predict_plant()
    ML-->>API: label (crop/weed/unknown) & confidence
    API->>CV: compute_bounding_box()
    CV-->>API: {x, y, width, height}
    
    alt weed (Yabani Ot)
        Note over API: Hedef Koordinatları Üretilir (target_x, target_y, target_nx, target_ny)
    else crop (Ekin)
        Note over API: Ekin Koruma Modu (target_point = null)
    end
    
    API->>DB: save_scan() -> Kalıcı Bulut Kaydı
    API-->>APP: JSON Yanıtı
    APP->>U: Sonuç Ekranı, Neon Glow Box ve Crosshair
```

---

## 🛠️ Teknoloji Yığını (Tech Stack)

| Katman | Teknoloji / Kütüphane | Kullanım Amacı |
| :--- | :--- | :--- |
| **Mobil Uygulama** | Flutter 3.x, Dart | Android & iOS uyumlu reaktif kullanıcı arayüzü |
| **Durum Yönetimi** | Provider | Reaktif mimari ve asenkron veri yönetimi |
| **Görsel İşleme (Mobil)**| `flutter_image_compress` | Kamera çekimlerini hücresel veri dostu boyuta indirme |
| **Backend Framework** | FastAPI (Python 3.11+) | Asenkron, yüksek verimli RESTful API ve Clean Architecture |
| **Sunucu Dağıtımı** | Render PaaS (Frankfurt EU) | 7/24 konteynerize mikroservis barındırma |
| **Uptime & Sağlık** | UptimeRobot | 5 dakikalık periyotlarla `@app.head` pingleme (Zero Cold Start) |
| **Bulut Veritabanı** | Supabase (PostgreSQL 15) | Kalıcı tarama geçmişi, IPv4 Connection Pooler ve RLS kalkanı |
| **Bilgisayarlı Görü** | `opencv-python-headless`, `scikit-image` | Morfolojik operasyonlar, HSV renk analizi, GLCM, HOG |
| **Makine Öğrenmesi** | `scikit-learn`, `joblib`, `numpy` | Random Forest sınıflandırıcı ve matris operasyonları |

---

## 👨‍💻 Geliştirici ve İletişim

**Ali Alperen Civil**  
*Lisans Bitirme Projesi & TÜBİTAK 2209-A Araştırmacısı*

* **GitHub:** [@AliAlperenCivil](https://github.com/AliAlperenCivil)
* **Proje Durumu:** Canlıda (Production Deployed) 🚀

---

<div align="center">
  <sub>© 2026 FloraScan Araştırma ve Geliştirme. Tüm hakları saklıdır.</sub>
</div>
