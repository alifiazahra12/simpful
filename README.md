![Python package](https://github.com/aresio/simpful/workflows/Python%20package/badge.svg?branch=master)
[![Documentation Status](https://readthedocs.org/projects/simpful/badge/?version=latest)](https://simpful.readthedocs.io/en/latest/?badge=latest)

# simpful
Pustaka Python untuk penalaran Fuzzy Logic, dirancang untuk menyediakan API yang sederhana serta ringan, sedekat mungkin dengan bahasa alaminya. Simpful mendukung penalaran Mamdani dan Sugeno dalam urutan apa pun, mengurai aturan fuzzy kompleks apa pun yang melibatkan operator (AND, OR, dan NOT), menggunakan himpunan fuzzy berbentuk arbitrer. 


## Contoh Penggunaan : 1

Menunjukkan bagaimana menentukan informasi tentang variabel linguistik, himpunan fuzzy, aturan fuzzy, dan nilai input ke Simpful. Baris terakhir kode mencetak hasil dari penalaran fuzzy.


```
import simpful as sf

# Model fuzzy sederhana menggambarkan bagaimana daya pemanas kompor gas bergantung pada suplai oksigen.

FS = sf.FuzzySystem()

# 
S_1 = sf.FuzzySet( points=[[0, 1.],  [1., 1.],  [1.5, 0]],          term="low_flow" )
S_2 = sf.FuzzySet( points=[[0.5, 0], [1.5, 1.], [2.5, 1], [3., 0]], term="medium_flow" )
S_3 = sf.FuzzySet( points=[[2., 0],  [2.5, 1.], [3., 1.]],          term="high_flow" )
FS.add_linguistic_variable("OXI", sf.LinguisticVariable( [S_1, S_2, S_3] ))

#
FS.set_crisp_output_value("LOW_POWER", 0)
FS.set_crisp_output_value("MEDIUM_POWER", 25)
FS.set_output_function("HIGH_FUN", "OXI**2")

# Mendefinisikan rules fuzzy
RULE1 = "IF (OXI IS low_flow) THEN (POWER IS LOW_POWER)"
RULE2 = "IF (OXI IS medium_flow) THEN (POWER IS MEDIUM_POWER)"
RULE3 = "IF (NOT (OXI IS low_flow)) THEN (POWER IS HIGH_FUN)"
FS.add_rules([RULE1, RULE2, RULE3])

#
FS.set_variable("OXI", .51)
print (FS.Sugeno_inference(['POWER']))
```

## Contoh Penggunaan : 2

Menunjukkan bagaimana memodelkan FIS menggunakan inferensi Mamdani. Ini juga menunjukkan beberapa fasilitas yang membuat pemodelan lebih ringkas dan jelas: Segitiga otomatis (yaitu, variabel linguistik pra-panggang dengan set fuzzy segitiga yang sama spasinya) dan deteksi otomatis metode inferensi.

```
from simpful import *

FS = FuzzySystem()

TLV = AutoTriangle(3, terms=['poor', 'average', 'good'], universe_of_discourse=[0,10])
FS.add_linguistic_variable("service", TLV)
FS.add_linguistic_variable("quality", TLV)

O1 = TriangleFuzzySet(0,0,13,   term="low")
O2 = TriangleFuzzySet(0,13,25,  term="medium")
O3 = TriangleFuzzySet(13,25,25, term="high")
FS.add_linguistic_variable("tip", LinguisticVariable([O1, O2, O3], universe_of_discourse=[0,25]))

FS.add_rules([
	"IF (quality IS poor) OR (service IS poor) THEN (tip IS low)",
	"IF (service IS average) THEN (tip IS medium)",
	"IF (quality IS good) OR (quality IS good) THEN (tip IS high)"
	])

FS.set_variable("quality", 6.5) 
FS.set_variable("service", 9.8) 

tip = FS.inference()
```
