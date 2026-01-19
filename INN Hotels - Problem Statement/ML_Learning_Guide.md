# Veri Bilimi ve Makine Öğrenmesi Eğitim Rehberi: Otel İptal Tahmini

Bu doküman, "INN Hotels Group" projesinde uyguladığımız teknikleri, modelleri ve stratejileri anlamanıza yardımcı olmak için hazırlanmış bir eğitim rehberidir.

---

## 1. Veriyi Anlamak (EDA - Exploratory Data Analysis)
Veri biliminin %80'i veriyi anlamaktır. Biz bu projede üç seviyeli bir analiz yaptık:

*   **Univariate (Tek Değişkenli):** Sadece bir sütuna odaklanır. (Örn: Ortalama fiyatlar ne kadar? Rezervasyonlar hangi aylarda yoğunlaşıyor?)
*   **Bivariate (İki Değişkenli):** İki sütun arasındaki ilişkiyi inceler. (Örn: Fiyat arttıkça iptal oranı artıyor mu? - *Korelasyon*)
*   **Multivariate (Çok Değişkenli):** Üç veya daha fazla değişkenin etkisini ölçer. (Örn: Pazar segmenti, ay ve fiyatın iptallere ortak etkisi.)

---

## 2. Veri Ön İşleme (Preprocessing) - "Veriyi Pişirmek"
Algoritmalar ham veriyi (yazıları, farklı ölçekteki sayıları) doğrudan anlayamazlar.

*   **Encoding (Kodlama):** Bilgisayarlar sayılarla konuşur. "Canceled" kelimesini `1`'e dönüştürerek modele neyi tahmin etmesi gerektiğini öğrettik.
*   **Scaling (Ölçeklendirme):** Bazı veriler çok büyük (Fiyat: 200€), bazıları çok küçüktür (Çocuk sayısı: 1). Ölçeklendirme (StandardScaler) yapmazsak, model fiyatı daha "önemli" sanırdı. Hepsini aynı teraziye koyduk.
*   **Feature Selection:** Tahmin gücü olmayan (Örn: Rezervasyon ID'si) sütunları modele kafa karıştırmasın diye attık.

---

## 3. Modelleri Tanıyalım

### A. K-Nearest Neighbors (KNN - En Yakın Komşu)
*   **Mantığı:** "Bana arkadaşını söyle, sana kim olduğunu söyleyeyim." Yeni bir rezervasyon geldiğinde, ona en çok benzeyen (en yakın) eski rezervasyonlara bakar. Onlar iptal edilmişse, bunu da iptal sayar.
*   **Zayıf Yönü:** Veri setimiz çok büyükse mesafe hesaplamak yavaşlayabilir.

### B. Naive Bayes
*   **Mantığı:** Tamamen olasılık odaklıdır. "Bugüne kadar online gelenlerin %30'u iptal ettiyse, bu yeni online gelende de %30 risk vardır" gibi bir mantık kurar.
*   **Güçlü Yönü:** Işık hızında çalışır.

### C. Support Vector Machine (SVM)
*   **Mantığı:** Verileri bir çizgiyle (veya düzlemle) ikiye bölmeye çalışır. İptal edenler ve etmeyenler arasına en geniş "güvenlik şeridini" (margin) çekmeye çalışır.
*   **Neden Yavaş?** Matematiksel olarak çok karmaşık bir denge kurmaya çalıştığı için büyük verilerde ağırlaşır.

---

## 4. Optimizasyon: Neden Örneklem (Sampling) Kullandık?
SVM yavaşladığında iki strateji uyguladık:

1.  **RandomizedSearchCV:** Tüm parametre kombinasyonlarını tek tek denemek yerine (Grid Search), rastgele ama akıllı seçimler yaparak en iyi sonucu daha hızlı bulduk.
2.  **Sampling (Örneklem %10):** 36.000 satırla deneme yapmak yerine, verinin karakterini yansıtan 3.600 satırla en iyi ayarları (tuning) bulduk. Ayarları bulunca, son modelimizi tüm veriyle eğittik. Bu, zaman kazanmanın profesyonel yoludur.

---

## 5. Başarıyı Nasıl Ölçeriz? (Metrikler)
Sadece "Doğruluk" (Accuracy) yetmez!

*   **Recall:** Gerçekten iptal edeceklerin kaçını yakaladık? (Otel için en önemli soru).
*   **Precision:** "İptal edecek" dediğimizde ne kadar haklıydık? (Gereksiz overbooking yapmamak için).
*   **F1-Score:** Bu ikisinin ortalamasıdır. Bizim ana hedefimiz budur.

---

## 6. Sonuç: Neden SVM Seçtik?
Cevap: **Genelleme (Generalization)**.
KNN çok iyi skorlar verdi ama eğitim verisini "ezberleme" (Overfitting) eğilimindeydi. SVM ise hem eğitimde hem testte dengeli sonuçlar vererek gerçek dünyadaki yeni rezervasyonları daha iyi tahmin edeceğini kanıtladı.

---

> **Eğitim Notu:** Bir veri bilimcisi sadece kod yazmaz; elindeki aracın (SVM, KNN) neden yavaşladığını bilir ve ona göre "Sampling" gibi stratejiler geliştirir. Bu proje ile bu profesyonel bakış açısını kazandınız!
