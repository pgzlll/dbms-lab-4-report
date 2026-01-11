# Deney Sonu Teslimatı

Sistem Programlama ve Veri Yapıları bakış açısıyla veri tabanlarındaki performansı öne çıkaran hususlar nelerdir?

Aşağıda kutucuk (checkbox) ile gösterilen maddelerden en az birini seçtiğiniz açık kaynak kodlu bir VT kaynak kodları üzerinde göstererek açıklayınız. Açıklama bölümüne kısaca metninizi yazıp, kod üzerinde gösterim videonuzun linkini en altta belirtilen kutucuğa yerleştiriniz.

- [X]  Seçtiğiniz konu/konuları bu şekilde işaretleyiniz. **!**
    
---

# 1. Sistem Perspektifi (Operating System, Disk, Input/Output)

### Disk Erişimi

- [ ]  **Blok bazlı disk erişimi** → block_id + offset
- [ ]  Rastgele erişim

### VT için Page (Sayfa) Anlamı

- [ ]  VT hangisini kullanır? **Satır/ Sayfa** okuması

---

### Buffer Pool

- [X]  Veritabanları, Sık kullanılan sayfaları bellekte (RAM) kopyalar mı (caching) ?

- [X]  LRU / CLOCK gibi algoritmaları
- [X]  Diske yapılan I/O nasıl minimize ederler?

# 2. Veri Yapıları Perspektifi

- [ ]  B+ Tree Veri Yapıları VT' lerde nasıl kullanılır?
- [ ]  VT' lerde hangi veri yapıları hangi amaçlarla kullanılır?
- [ ]  Clustered vs Non-Clustered Index Kavramı
- [ ]  InnoDB satırı diskte nasıl durur?
- [ ]  LSM-tree (LevelDB, RocksDB) farkı
- [ ]  PostgreSQL heap + index ayrımı

DB diske yazarken:

- [ ]  WAL (Write Ahead Log) İlkesi
- [ ]  Log disk (fsync vs write) sistem çağrıları farkı

---

# Özet Tablo

| Kavram      | Bellek          | Disk / DB      |
| ----------- | --------------- | -------------- |
| Adresleme   | Pointer         | Page + Offset  |
| Hız         | O(1)            | Page IO        |
| PK          | Yok             | Index anahtarı |
| Veri yapısı | Array / Pointer | B+Tree         |
| Cache       | CPU cache       | Buffer Pool    |

---

# Video [Linki](https://youtu.be/sFRj1hKTDzM)  

---

# Açıklama 

PostgreSQL veritabanı sisteminin açık kaynak kodu, sistem programlama ve veri yapıları açısından bu çalışmada incelenmiştir. PostgreSQL’in bellek yönetimi ve disk erişim maliyetlerini azaltmaya yönelik mekanizmaları inceleme kapsamında, kaynak kod üzerindeki ilgili bölümler üzerinden gösterilerek açıklanmıştır. Özellikle bufmgr.c dosyasında yer alan ReadBuffer fonksiyonu aracılığıyla, diskten okunan sayfaların RAM’de bulunan buffer pool içerisinde tutulduğu ve tekrar eden erişimlerde disk yerine bellek kopyalarının kullanıldığı gösterilmiştir. Bu yapı veritabanlarında caching mekanizmasının temelini oluşturmaktadır.

Ayrıca sayfa üzerinde yapılan güncellemelerde MarkBufferDirty fonksiyonu kullanılarak ilgili buffer’ın “dirty” olarak işaretlendiği ve fiziksel disk yazma işlemlerinin anında gerçekleştirilmediği görülmüştür. Yazma işlemleri buffer replacement veya checkpoint aşamasına kadar ertelendiği için çok sayıda küçük disk yazması yerine daha az sayıda toplu yazma yapılmakta bu durum da disk I/O maliyetlerini önemli ölçüde azaltmaktadır.

Buffer pool dolu olduğunda hangi sayfanın bellekten çıkarılacağına karar veren replacement stratejisi freelist.c dosyasında incelenmiştir. PostgreSQL’in klasik LRU algoritması yerine daha düşük senkronizasyon maliyetine sahip olan CLOCK-sweep yaklaşımını kullandığı kaynak kodda yer alan clock hand (nextVictimBuffer) yapısı ve kullanım sayacına dayalı “second chance” mantığı üzerinden gösterilmiştir. Bu yaklaşım sık kullanılan sayfaların bellekte tutulmasını sağlarken nadiren erişilen sayfaların victim olarak seçilmesine imkân tanımaktadır. Sonuç olarak bu mekanizmalar PostgreSQL’in bellek kullanımını verimli hâle getirerek disk erişimini minimize etmesini sağlamaktadır.

## VT Üzerinde Gösterilen Kaynak Kodları

bufmgr.c: [Linki](https://github.com/postgres/postgres/blob/master/src/backend/storage/buffer/bufmgr.c) \
freelist.c: [Linki](https://github.com/postgres/postgres/blob/master/src/backend/storage/buffer/bufmgr.c) \ 

