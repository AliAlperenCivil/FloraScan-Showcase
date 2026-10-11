<div align="center">

<img src="assets/logo.png" alt="FloraScan Logo" width="110" />

# FloraScan

### Yapay Zeka Destekli Hassas Tarım ve Robotik Hedefleme Sistemi

Ekin ve yabani otları gerçek zamanlı tespit eden, pülverizatör ve otonom ilaçlama nozulları için milimetrik hedef koordinatı üreten uçtan uca tarımsal yapay zeka ekosistemi.

<p align="center">
  <img src="assets/badge_tubitak.svg" alt="TÜBİTAK 2209-A" />
  <img src="https://img.shields.io/badge/Akademik-Bitirme_Tezi-1f4287?style=flat-square" alt="Bitirme Tezi" />
  <img src="https://img.shields.io/badge/Model-97%25_Dogruluk-2e7d32?style=flat-square" alt="Model Doğruluğu" />
  <img src="https://img.shields.io/badge/Cross_Validation-96.8%25-brightgreen?style=flat-square" alt="Cross Validation" />
  <img src="https://img.shields.io/badge/Flutter-3.x-02569B?style=flat-square&logo=flutter&logoColor=white" alt="Flutter" />
  <img src="https://img.shields.io/badge/FastAPI-Python-009688?style=flat-square&logo=fastapi&logoColor=white" alt="FastAPI" />
  <img src="https://img.shields.io/badge/Supabase-PostgreSQL-3ECF8E?style=flat-square&logo=supabase&logoColor=white" alt="Supabase" />
  <img src="https://img.shields.io/badge/OpenCV-Computer_Vision-5C3EE8?style=flat-square&logo=opencv&logoColor=white" alt="OpenCV" />
</p>

</div>

---

> **Gizlilik ve Fikri Mülkiyet Bildirimi (Intellectual Property Notice):**  
> Bu proje; **TÜBİTAK 2209-A Üniversite Öğrencileri Araştırma Projeleri Destekleme Programı** ve **Mühendislik Lisans Bitirme Tezi** kapsamında geliştirilmektedir. Akademik yayın, patent ve tez teslim süreçlerinin korunması amacıyla sistemin çekirdek kaynak kodları bu vitrin deposunda açık kaynak olarak sunulmamaktadır. Bu repository; projenin sistem mimarisini, öznitelik mühendisliği yöntemlerini, model başarım metriklerini ve arayüzünü sergileyen resmi vitrin deposudur.

---

## Projeye Genel Bakış

Geleneksel tarımsal mücadelede yabani otlar tüm tarlaya homojen ve aşırı miktarda kimyasal herbisit (tarım ilacı) püskürtülerek yok edilmeye çalışılmaktadır. Bu durum; toprak kirliliğine, yeraltı sularının zehirlenmesine, kültür bitkilerinin stres altına girmesine ve yüksek kimyasal girdi maliyetlerine yol açmaktadır.

FloraScan, mobil kamera aracılığıyla tarladaki bitki örtüsünü anlık olarak analiz eder:

1. **Sınıflandırma:** Bitkinin Kültür Bitkisi (Crop) mi yoksa Yabani Ot (Weed) mu olduğunu %97 doğrulukla sınıflandırır.
2. **Morfolojik Segmentasyon:** Hedef bitki gövdesini çevreleyen dinamik sınırlayıcı kutuyu (Bounding Box) hesaplar.
3. **Hassas Hedefleme:** Tespit edilen bitki yabani ot ise, bitkinin geometrik ağırlık merkezini çıkararak ilaçlama nozullarının kilitlenebileceği piksel ve normalize koordinatları ($X, Y, nX, nY$) üretir.
4. **Ekin Koruma Modu:** Tespit edilen bitki bir kültür bitkisi ise, ilaçlama mekanizması bloke edilir (`target_point = null`) ve ekin koruma kalkanı devreye girer.

---

## Mobil Kullanıcı Arayüzü ve Hedefleme Akışı

Saha koşulları ve doğrudan güneş ışığı altında optimum okunabilirlik sağlamak amacıyla yüksek kontrastlı arayüz hiyerarşisi uygulanmıştır:

<div align="center">
  <table>
    <tr>
      <th align="center" width="50%">Ana Ekran ve Kamera/Galeri Seçimi</th>
      <th align="center" width="50%">Analiz Sonucu, Sınırlayıcı Kutu ve Hedef Koordinatı</th>
    </tr>
    <tr>
      <td align="center">
        <img src="assets/home_screen.jpg" width="340" alt="FloraScan Ana Ekran" />
      </td>
      <td align="center">
        <img src="assets/result_screen.jpg" width="340" alt="FloraScan Analiz Sonucu" />
      </td>
    </tr>
    <tr>
      <td align="center"><em>Çekim ve galeri seçim arayüzü</em></td>
      <td align="center"><em>Yeşil sınırlayıcı kutu, kırmızı nişangah ve milimetrik koordinat kartı</em></td>
    </tr>
  </table>
</div>

* **Görüntü Optimizasyonu:** Çekilen yüksek çözünürlüklü ham fotoğraflar (10+ MB), istemci tarafında $1024 \times 1024$ JPEG %85 formatına sıkıştırılarak hücresel veri tüketimi ve ağ gecikmesi en aza indirilir.
* **Görsel Geri Bildirim:** Analiz edilen yabani ot üzerinde yeşil sınırlayıcı kutu ve kırmızı hedef artı işareti (crosshair) dinamik olarak çizilir.
* **Telemetri ve Koordinat Kartı:** Piksel ($X: 439\text{ px}, Y: 544\text{ px}$) ve normalize koordinatlar ($0.5716, 0.5313$) ekranda kullanıcıya ve donanım arayüzüne sunulur.

---

## Bilimsel ve Mühendislik Yaklaşımı

### 9 Boyutlu Hibrit Öznitelik Vektörü

Yüksek bellek tüketimine ve mobil cihazlarda aşırı ısınmaya yol açan derin öğrenme mimarileri yerine, yaprak morfolojisini ve dokusunu doğrudan modelleyen 9 boyutlu hibrit bir matematiksel öznitelik vektörü geliştirilmiştir:

$$\vec{V} = [H_{mean}, S_{mean}, V_{mean}, Green_{ratio}, Contours, Area, Contrast_{GLCM}, Homogeneity_{GLCM}, HOG_{mean}]$$

```mermaid
flowchart LR
    IMG["Girdi Goruntusu<br/>(128x128 Piksel)"] --> PRE["Renk ve Uzay Donusumu<br/>(HSV + Gri Seviye)"]
    
    PRE --> F1["1-3. HSV Renk Ortalamalari<br/>[H_mean, S_mean, V_mean]"]
    PRE --> F2["4. Yesil Maske Alan Orani<br/>[Green Ratio]"]
    PRE --> F3["5-6. Morfolojik Kontur ve Alan<br/>[Contours, Area]"]
    PRE --> F4["7-8. GLCM Doku Analizi<br/>[Contrast, Homogeneity]"]
    PRE --> F5["9. HOG Gradyan Kenar Histogrami<br/>[HOG Mean]"]
    
    F1 & F2 & F3 & F4 & F5 --> VEC["9 BOYUTLU HIBRIT VEKTOR"]
    VEC --> MODEL["Random Forest Siniflandirici<br/>(n_estimators=200, max_depth=10)"]
    MODEL --> OUT["Sinif: Crop / Weed / Unknown<br/>Guven Skoru: %0 - %100"]
```

### Model Karşılaştırma ve Başarım Tablosu

| Algoritma | Parametreler | Test Doğruluğu | Çıkarım Karakteristiği |
| :--- | :--- | :--- | :--- |
| **Random Forest (Seçilen)** | `n_estimators=200, max_depth=10` | **%97.00** | **En yüksek genelleme, mikrosaniyeler mertebesinde çıkarım süresi, aşırı öğrenmeye karşı direnç.** |
| **Gradient Boosting (GBDT)** | `n_estimators=100, lr=0.1` | %95.20 | Yüksek doğruluk; sıralı ağaç yapısı nedeniyle daha uzun çıkarım süresi. |
| **Destek Vektör Makineleri (SVM)** | `RBF Kernel, C=1.0` | %94.80 | Kararlı karar sınırları; yüksek boyutlu uzayda güvenilir ayrım. |
| **Lojistik Regresyon** | `max_iter=1000` | %88.40 | Doğrusal sınır; karmaşık yaprak dokusu ve ışık değişimlerinde sınırlı başarım. |

* **5-Katlı Çapraz Doğrulama (5-Fold Cross Validation):** %96.8 kararlı doğruluk.
* **Model Dosya Boyutu:** Yalnızca 1.7 MB (`flora_model.joblib`), bulut ve uç cihazlarda anlık bellek yükleme imkanı.

---

## Uçtan Uca Sistem Mimarisi

Sistem; mobil istemci, bulut API, görüntü işleme motoru, makine öğrenmesi çıkarım birimi ve ilişkisel veritabanından oluşan dağıtık bir yapı üzerinde çalışır:

```mermaid
sequenceDiagram
    autonumber
    actor U as Kullanici / Ciftci
    participant APP as Flutter Mobil Istemci
    participant API as FastAPI Cloud API (Render EU)
    participant CV as CV Motoru (OpenCV & Skimage)
    participant ML as ML Cikarim (Random Forest)
    participant DB as Supabase PostgreSQL (RLS)

    U->>APP: Kamera ile Cek / Galeriden Sec
    Note over APP: Gorsel Boyutlandirma ve Sikistirma (1024x1024)
    APP->>API: POST /predict (Multipart/form-data via HTTPS)
    API->>CV: extract_features_from_array()
    CV-->>API: 9 Boyutlu Oznitelik Vektoru
    API->>ML: predict_plant()
    ML-->>API: Sinif (crop/weed/unknown) ve Guven Skoru
    API->>CV: compute_bounding_box()
    CV-->>API: Sınır Kutusu Koordinatlari (x, y, w, h)
    
    alt Weed (Yabani Ot)
        Note over API: Hedefleme Noktasi Hesaplanir (target_x, target_y, target_nx, target_ny)
    else Crop (Kultur Bitkisi)
        Note over API: Ekin Koruma Modu (target_point = null)
    end
    
    API->>DB: save_scan() ile Kalici Veritabani Kaydi
    API-->>APP: JSON Yaniti
    APP->>U: Sinirlayici Kutu, Nisangah ve Hedef Kartini Goster
```

---

## Teknoloji Yığını

| Katman | Teknoloji / Kütüphane | Görev ve Sorumluluk |
| :--- | :--- | :--- |
| **Mobil Uygulama** | Flutter 3.x, Dart | Çapraz platform istemci, reaktif kullanıcı arayüzü |
| **Durum Yönetimi** | Provider | Durum yönetimi ve asenkron veri akışı |
| **Görüntü İşleme (İstemci)** | `flutter_image_compress` | Kamera çekimlerini ağ transferi öncesi optimize etme |
| **Backend Framework** | FastAPI (Python 3.11+) | Asenkron RESTful API mimarisi ve mikroservis yönetimi |
| **Bulut Dağıtımı** | Render PaaS (Frankfurt EU) | Konteynerize üretim sunucusu barındırma |
| **Sağlık İzleme** | UptimeRobot | 5 dakikalık periyotlarla HTTP HEAD denetimi (Soğuk Başlatma Önleme) |
| **Veritabanı** | Supabase (PostgreSQL 15) | Kalıcı tarama telemetrisi, IPv4 Connection Pooler, RLS güvenliği |
| **Bilgisayarlı Görü** | `opencv-python-headless`, `scikit-image` | Renk dönüşümleri, morfolojik filtreleme, GLCM, HOG analizi |
| **Makine Öğrenmesi** | `scikit-learn`, `joblib`, `numpy` | Random Forest sınıflandırma ve tensör işlemleri |

---

## Proje ve İletişim

**Ali Alperen Civil**  
Lisans Bitirme Projesi & TÜBİTAK 2209-A Araştırmacısı  

* **GitHub Profili:** [github.com/AliAlperenCivil](https://github.com/AliAlperenCivil)  
* **Canlı API Durumu:** Aktif (Production Deployed)  

---

<div align="center">
  <sub>FloraScan Arastirma ve Gelistirme Projesi. Tum haklari saklidir.</sub>
</div>
