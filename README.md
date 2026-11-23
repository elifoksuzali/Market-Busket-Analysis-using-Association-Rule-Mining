# Market Basket Analysis using Association Rule Mining

Birliktelik kuralı madenciliği (Association Rule Mining) ile **Market Sepeti Analizi**.
Bu proje, perakende/alışveriş işlem verileri üzerinde **Apriori** algoritmasını kullanarak
"X ürününü alan müşteri Y ürününü de alır" tipinde kurallar çıkarır.

---

## 📌 Proje Amacı

Market sepeti analizi, müşterilerin aynı alışverişte birlikte satın aldığı ürünleri
keşfetmeye yarayan bir veri madenciliği tekniğidir. Elde edilen kurallar şu amaçlarla kullanılır:

- **Çapraz satış (cross-selling):** Birlikte alınan ürünleri önerme
- **Raf/yerleşim düzeni:** İlişkili ürünleri yakın konumlandırma
- **Kampanya & indirim:** Ürün paketleri (bundle) oluşturma
- **Öneri sistemleri:** "Bunu alanlar şunu da aldı" tavsiyeleri

---

## 🧠 Temel Kavramlar

Birliktelik kuralları `{A} → {B}` biçiminde yazılır ve üç ölçütle değerlendirilir:

| Metrik | Anlamı | Formül |
|--------|--------|--------|
| **Support (Destek)** | A ve B'nin birlikte görülme sıklığı | `P(A ∩ B)` |
| **Confidence (Güven)** | A alındığında B'nin alınma olasılığı | `P(B \| A)` |
| **Lift (Kaldıraç)** | Kuralın rastlantıdan ne kadar güçlü olduğu | `Confidence / P(B)` |

> Lift > 1 ise A ve B arasında pozitif bir ilişki vardır.

---

## 📂 Proje Yapısı

```
Market-Busket-Analysis-using-Association-Rule-Mining/
│
├── Only Product Purchase dada/        # Sadece ürün adlarından oluşan işlemler
│   ├── Data.csv                       # ~7500 alışveriş işlemi (ürün listeleri)
│   ├── Market Basket Optimization.ipynb
│   ├── code.py                        # apyori ile kısa örnek script
│   └── aprior.py                      # Apriori algoritmasının saf-Python uygulaması
│
├── Retails Shops Data/                # Gerçek perakende veri setleri
│   ├── GroceryStoreDataSet.csv        # Küçük market veri seti
│   ├── Online Retail.csv / .xlsx      # UCI Online Retail veri seti
│   ├── Analysis of Retails Shops data.ipynb
│   ├── Apriori Algorithm and it's implementation.ipynb
│   └── TransactionEncoder - mlxtend.pdf   # mlxtend referans dokümanı
│
├── LICENSE
└── README.md
```

İki farklı yaklaşım gösterilmektedir:

1. **`Only Product Purchase dada/`** → [`apyori`](https://pypi.org/project/apyori/)
   kütüphanesi ile hızlı kural çıkarımı.
2. **`Retails Shops Data/`** → [`mlxtend`](http://rasbt.github.io/mlxtend/) kütüphanesi ile
   `TransactionEncoder` + `apriori` + `association_rules` akışı.

---

## ⚙️ Kurulum

### 1. Depoyu klonlayın

```bash
git clone https://github.com/elifoksuzali/Market-Busket-Analysis-using-Association-Rule-Mining.git
cd Market-Busket-Analysis-using-Association-Rule-Mining
```

### 2. (Önerilir) Sanal ortam oluşturun

```bash
python -m venv venv
# Windows
venv\Scripts\activate
# macOS / Linux
source venv/bin/activate
```

### 3. Gerekli kütüphaneleri yükleyin

```bash
pip install pandas numpy matplotlib mlxtend apyori jupyter openpyxl
```

> `openpyxl`, `Online Retail.xlsx` dosyasını okumak için gereklidir.

---

## ▶️ Çalıştırma

### Jupyter Notebook ile (önerilen)

```bash
jupyter notebook
```

Ardından ilgili `.ipynb` dosyasını açıp hücreleri sırayla çalıştırın:

- `Retails Shops Data/Apriori Algorithm and it's implementation.ipynb`
- `Retails Shops Data/Analysis of Retails Shops data.ipynb`
- `Only Product Purchase dada/Market Basket Optimization.ipynb`

### Python scripti ile

```bash
cd "Only Product Purchase dada"
python code.py
```

> Not: `code.py` dosyası `Market_Basket_Optimisation.csv` adını bekler. Depodaki dosya
> `Data.csv` olarak adlandırılmıştır; scriptteki dosya adını `Data.csv` olarak güncelleyin
> veya CSV'yi yeniden adlandırın.

---

## 🔬 Nasıl Çalışır? (mlxtend akışı)

```python
import pandas as pd
from mlxtend.preprocessing import TransactionEncoder
from mlxtend.frequent_patterns import apriori, association_rules

# 1. İşlemleri liste listesi haline getir
transactions = [
    ["MILK", "BREAD", "BISCUIT"],
    ["BREAD", "MILK", "BISCUIT", "CORNFLAKES"],
    ...
]

# 2. One-hot (True/False) matrise dönüştür
te = TransactionEncoder()
te_array = te.fit(transactions).transform(transactions)
df = pd.DataFrame(te_array, columns=te.columns_)

# 3. Sık geçen ürün kümelerini bul
frequent_itemsets = apriori(df, min_support=0.2, use_colnames=True)

# 4. Birliktelik kurallarını üret
rules = association_rules(frequent_itemsets, metric="lift", min_threshold=1)
print(rules[["antecedents", "consequents", "support", "confidence", "lift"]])
```

### apyori akışı

```python
from apyori import apriori

rules = apriori(
    transactions,
    min_support=0.003,
    min_confidence=0.2,
    min_lift=3,
    min_length=2,
)
results = list(rules)
```

---

## 📊 Kullanılan Veri Setleri

| Veri Seti | Açıklama |
|-----------|----------|
| **Data.csv** | ~7.500 alışveriş işlemi; her satır bir sepetteki ürünler |
| **GroceryStoreDataSet.csv** | Küçük ölçekli market işlemleri (demo amaçlı) |
| **Online Retail.csv / .xlsx** | UCI Online Retail — gerçek e-ticaret fatura verileri |

---

## 🛠️ Kullanılan Teknolojiler

- **Python 3**
- **pandas**, **numpy** — veri işleme
- **mlxtend** — Apriori & association rules
- **apyori** — alternatif Apriori uygulaması
- **matplotlib** — görselleştirme
- **Jupyter Notebook** — analiz ortamı

---

## 📄 Lisans

Bu proje [LICENSE](LICENSE) dosyasında belirtilen lisans kapsamında dağıtılmaktadır.

---

## 👤 Yazar

**Elif Öksüzali** — [GitHub](https://github.com/elifoksuzali)
