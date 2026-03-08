# Veri Bilimi ve Makine Öğrenmesi Eğitim Rehberi: Otel İptal Tahmini

Bu doküman, "INN Hotels Group" projesinde uyguladığımız teknikleri, modelleri ve stratejileri derinlemesine anlamanıza yardımcı olmak için hazırlanmış kapsamlı bir eğitim rehberidir.

---

## 1. Veriyi Anlamak (EDA - Exploratory Data Analysis)
Veri biliminde başarının %80'i veriyi doğru analiz etmekten geçer. Bu projede üç farklı seviyede analiz uyguladık:

### 📊 Univariate (Tek Değişkenli) Analiz
Sadece tek bir sütunun (değişkenin) davranışına odaklanırız.
*   **Örnek:** Rezervasyonların hangi aylarda yoğunlaştığını inceledik ve **Ekim** ayının en yoğun ay olduğunu gördük.
*   **Teknik Not:** Bu analiz, verideki çarpıklıkları (skewness) ve uç değerleri (outliers) ilk aşamada görmemizi sağlar.

### 📈 Bivariate (İki Değişkenli) Analiz
İki değişken arasındaki ilişkiyi veya korelasyonu inceleriz.
*   **Örnek:** Rezervasyonun yapıldığı tarih ile varış tarihi arasındaki sürenin (**Lead Time**) iptal durumu üzerindeki etkisine baktık.
*   **Teknik Not:** Bu, hangi özelliklerin (features) hedef değişkenimiz üzerinde daha etkili olduğunu anlamamıza yardımcı olur.

### 🕸️ Multivariate (Çok Değişkenli) Analiz
Üç veya daha fazla değişkenin birbiriyle etkileşimini inceleriz.
*   **Örnek:** Pazar segmenti, ay ve oda fiyatının iptaller üzerindeki ortak etkisini analiz ettik.

---

## 2. Veri Ön İşleme (Preprocessing): "Veriyi Modellemeye Hazırlamak"
Algoritmalar ham veriyi (kelimeler, farklı ölçekteki sayılar) doğrudan işleyemezler.

### 🔠 Kodlama (Encoding)
Bilgisayarların anlayabileceği tek dil sayılardır.
*   **Label Encoding:** 'Canceled' kelimesini `1`, 'Not Canceled' kelimesini `0` yaptık.
*   **One-Hot Encoding:** 'Online', 'Offline' gibi kategorileri ayrı ayrı sütunlara (dummy variables) böldük. Bu, modelin "Online > Offline" gibi yanlış bir matematiksel sıralama yapmasını önler.

### ⚖️ Ölçeklendirme (Feature Scaling)
*   **Sorun:** KNN ve SVM gibi modeller "mesafe" hesaplar. Eğer fiyat 200€, çocuk sayısı 1 ise; model fiyatı 200 kat daha önemli sanabilir.
*   **Çözüm:** `StandardScaler` kullanarak tüm değerleri ortalaması 0, varyansı 1 olacak şekilde aynı "teraziye" koyduk.

---

## 3. Modelleri Tanıyalım: Teknik Detaylar

### 🗺️ K-Nearest Neighbors (KNN)
*   **Mantık:** "Bana arkadaşını söyle, sana kim olduğunu söyleyeyim." Yeni bir rezervasyonu, ona en çok benzeyen eski kayıtlara bakarak sınıflandırır.
*   **İpucu:** Basittir ama veri seti çok büyüdüğünde her nokta için mesafe hesapladığı için yavaşlayabilir.

### 🎲 Naive Bayes
*   **Mantık:** Bayes Teoremi'ne dayanır; özelliklerin birbirinden tamamen bağımsız olduğunu varsayarak olasılık hesabı yapar.
*   **İpucu:** İnanılmaz hızlıdır ve az veriyle bile iyi baseline sonuçlar verebilir; ancak "bağımsızlık" varsayımı gerçek hayatta nadiren tam olarak karşılanır.

### 🛡️ Support Vector Machine (SVM)
*   **Mantık:** Verileri birbirinden ayıran en geniş "güvenlik şeridini" (margin) bulmaya çalışır.
*   **Kritik Bilgi:** SVM karmaşıklığı, veri sayısı arttıkça kübik olarak artar ($O(n^3)$). 36.000 satırda bu yüzden çok yavaşladı (46 dakikalık bekleme süresinin sebebi buydu).

---

## 4. Profesyonel Strateji: Örneklem (Sampling) ve Eğitim
Gerçek dünyada büyük veriyle çalışırken "beklemek" bir seçenek değildir, "optimize etmek" gerekir.

### 🧪 Stratified Sampling (Tabakalı Örneklem)
Hyperparameter tuning (en iyi ayarları bulma) aşamasında verinin **%10'unu (3.600 satır)** kullandık.
*   **Neden Stratified?** Çünkü bu yöntem, örneklemdeki 'İptal' oranının ana veridekiyle birebir aynı kalmasını sağlar. Yani çorbanın tadına bakarken her yerini karıştırmış oluruz.

### ⚡ Hızlı Arama, Güçlü Eğitim
1.  **Arama:** En iyi parametreleri (C, kernel vb.) %10'luk küçük veride `RandomizedSearchCV` ile hızlıca bulduk.
2.  **Eğitim:** En iyi parametreleri karar verdikten sonra, final modelimizi **tüm veriyle (%100)** eğittik. Böylece hem zaman kazandık hem de model performansından ödün vermedik.

---

## 5. Başarı Metrikleri: Sadece "Doğruluk" Yetmez!
Eğer veride iptal oranı dengesizse, her şeye "iptal değil" diyen bir model bile yüksek doğruluk (Accuracy) verebilir. Bu yüzden şunlara baktık:

*   **Recall (Duyarlılık):** Gerçekten iptal edeceklerin yüzde kaçını yakaladık? (Otel için en önemli soru).
*   **Precision (Keskinlik):** "İptal edecek" dediğimizde ne kadar haklıydık? (Gereksiz aşırı oda satışını önlemek için).
*   **F1-Score:** Bu ikisinin dengeli bir ortalamasıdır. Bizim ana başarı kriterimiz budur.

---

> **Eğitim Notu:** İyi bir veri bilimcisi sadece kod yazan değil; SVM gibi bir model yavaşladığında "Sampling" gibi optimizasyon tekniklerini kullanarak işi bitirebilen kişidir. Bu proje ile bu profesyonel vizyonu kazandınız!
