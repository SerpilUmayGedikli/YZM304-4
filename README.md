# Metin Duygu Sınıflandırması ve Boyut İndirgeme Deneyleri

## Giriş

Bu çalışmada, sosyal medya üzerinde yer alan metinlerin olumlu ya da olumsuz duygu taşıyıp taşımadığını sınıflandırmak amacıyla bir makine öğrenmesi uygulaması gerçekleştirilmiştir. Duygu sınıflandırması, özellikle kriz anlarında kamuoyu analizi, pazarlama stratejileri ve toplumsal eğilimlerin belirlenmesinde önemli bir rol oynamaktadır. Projede kullanılan veriler, Twitter’dan toplanmış İngilizce tweet’lerden oluşmakta ve metinlerin içerdiği duyguları pozitif ya da negatif olarak etiketlemeyi hedeflemektedir. Yüksek boyutlu metin verisinin modellenmesini kolaylaştırmak ve sınıflandırma başarımını artırmak amacıyla boyut indirgeme tekniklerinden yararlanılmış, özellikle Autoencoder mimarisi kullanılarak gizli temsiller elde edilmiştir. Çalışmada ayrıca geleneksel sınıflandırma algoritmaları olan Logistic Regression ve Support Vector Machine (SVM) karşılaştırmalı olarak test edilmiştir.

## Yöntem

### Veri Setleri ve Ön İşleme

İki farklı kaynak kullanılmıştır:

1. **COVID-19 Vaccine Misinformation Tweets Dataset (Zenodo):** 2020–2021 yıllarında paylaşılan, aşı karşıtı içerikler içeren tweet’lerden oluşmaktadır. Bu veri etiketsizdir.
2. **Sentiment140 Dataset:** 160.000 tweet içerir ve duygusal etiketler (0=negatif, 2=nötr, 4=pozitif) içerir.

Bu veri setlerinden alınan örneklerle `zenodo_labeled.csv` adlı nihai veri seti oluşturulmuştur. Sadece pozitif (5712) ve negatif (4489) olmak üzere iki sınıf ele alınmış, `clean_text` sütunu kullanılarak ön işlemden geçirilmiş metinler analiz edilmiştir. Metinler TF-IDF yöntemi ile 5000 boyutlu vektörlere dönüştürülmüştür.

### Autoencoder ile Boyut İndirgeme

TF-IDF vektörlerinin yüksek boyutluluğu nedeniyle işlem verimliliğini artırmak ve görselleştirme yapmak amacıyla katmanları tam bağlantılı (Dense) bir Autoencoder mimarisi uygulanmıştır. Encoder kısmı, 5000 boyuttan 128 boyuta indirgeme yapacak şekilde tasarlanmıştır. Model 20 epoch boyunca eğitilmiş, ortalama yeniden yapılandırma kaybı 0.00068 düzeyinde seyretmiştir.

### Sınıflandırma Algoritmaları

Hem ham TF-IDF vektörleri hem de Autoencoder çıktısı olan 128 boyutlu gizli temsiller, iki farklı sınıflandırıcı ile test edilmiştir:

- Logistic Regression
- Support Vector Machine (SVM)

Modellerin başarımları doğruluk (accuracy), precision, recall, F1-score ve karmaşıklık matrisi gibi metriklerle değerlendirilmiştir.

## Sonuçlar

### TF-IDF ile Sınıflandırma (Boyut İndirgeme Olmadan)

| Model               | Accuracy | Precision (Neg) | Recall (Neg) | F1 (Neg) | Precision (Pos) | Recall (Pos) | F1 (Pos) |
|---------------------|----------|------------------|---------------|-----------|------------------|---------------|-----------|
| Logistic Regression | 0.57     | 0.54             | 0.20          | 0.29      | 0.58             | 0.87          | 0.69      |
| SVM                 | 0.56     | 0.00             | 0.00          | 0.00      | 0.56             | 1.00          | 0.72      |

- SVM, negatif sınıfı neredeyse hiç tanımamış.
- Logistic Regression, pozitif sınıfta daha başarılı.

### Autoencoder Sonrası Sınıflandırma (128 Boyuta İndirgenmiş Veri)

| Model               | Accuracy | Precision (Neg) | Recall (Neg) | F1 (Neg) | Precision (Pos) | Recall (Pos) | F1 (Pos) |
|---------------------|----------|------------------|---------------|-----------|------------------|---------------|-----------|
| Logistic Regression | 0.61     | 0.55             | 0.62          | 0.58      | 0.67             | 0.60          | 0.63      |
| SVM                 | 0.61     | 0.56             | 0.60          | 0.58      | 0.67             | 0.63          | 0.65      |

- Boyut indirgeme ile modeller daha dengeli hale gelmiş ve doğruluk artmıştır.
- Negatif sınıfın tanınması anlamlı şekilde iyileşmiştir.

## Tartışma

Boyut indirgeme, sadece hesaplama yükünü azaltmakla kalmamış; sınıflandırma doğruluğunu da artırmıştır. Özellikle yüksek boyutlu veri ile çalışan modellerde negatif sınıfın öğrenilmesinde zorluk yaşanmış, bu durum Autoencoder yardımıyla aşılmıştır. Eğitim sürecinde kaybın çok düşük seviyelere inmesi, modelin veriye fazla uyum sağladığını (overfitting) gösterebilir. Bu sebeple epoch sayısının dikkatli seçilmesi gerektiği görülmüştür.

SVM, ham veride negatif sınıfı tanımakta yetersiz kalırken, Autoencoder sonrası bu sınıfı da öğrenebilmeye başlamıştır. Bu, boyut indirgeme işleminin sınıflar arası ayırımı kolaylaştırıcı etkisini göstermektedir.

## Gelecek Çalışmalar

- BERT, RoBERTa gibi önceden eğitilmiş dil modelleriyle embedding alınarak benzer bir deneysel tasarım tekrar edilebilir.
- Latent uzayda Clustering ya da Anomaly Detection gibi ileri seviye analizler yapılabilir.
- Denoising Autoencoder ile metin gürültülerine karşı daha dayanıklı temsil öğrenimi denenebilir.
- Model başarımı daha karmaşık neural mimarilerle (CNN+RNN, attention tabanlı yaklaşımlar vb.) test edilebilir.

## Referanslar

1. Sentiment140 Dataset. http://help.sentiment140.com/for-students/
2. Zenodo COVID-19 Vaccine Tweets Dataset. https://zenodo.org/record/4562049
3. Géron, A. (2019). *Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow*. O'Reilly Media.
4. Chollet, F. (2017). *Deep Learning with Python*. Manning Publications.

---
