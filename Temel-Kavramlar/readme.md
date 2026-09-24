#  SQL Server İşlemleri Rehberi

Bu rehber, Microsoft SQL Server kullanarak veritabanı yönetiminin temellerini daha iyi anlayabilmek hazırlanmıştır. Adım adım bir veritabanı oluşturacak, tablolarımızı ayarlayacak ve veri ekleme/güncelleme işlemlerini gerçekleştireceğiz.

---

## 1. Veritabanı Oluşturma (CREATE DATABASE)

SQL Server'da çalışmaya başlamadan önce tıpkı bilgisayarımızda yeni bir klasör açar gibi verilerimizi koyacağımız bir **veritabanı** oluşturmamız gerekir. Bu işlem `CREATE DATABASE` komutu ile yapılır.
```sql
-- "Okul" adında yeni bir veritabanı oluşturuyoruz.
CREATE DATABASE Okul;

-- Oluşturduğumuz veritabanını kullanmaya başlamak için USE komutunu veririz.
-- Bu komut, bundan sonraki işlemlerin "Okul" içinde yapılacağını belirtir.
```

Oluşturduğumuz bu veritabanının içine girip artık burada işlem yapacağız demek için `USE` komutunu kullanıyoruz.
```sql
USE Okul;
```
---

## 2. Tablo Oluşturma, Sütun ve Kolon Ayarları (CREATE TABLE)
Veritabanını kurduk, peki verileri buraya nasıl atacağız? Tabii ki tablolar oluşturarak.
Veritabanını oluşturduktan sonra, verilerimizi düzenli bir şekilde tutmak için tablolar oluştururuz. Tablolar, satır ve sütunlardan (kolonlardan) oluşur. Sütunları tanımlarken verinin tipini (metin, sayı, tarih vb.) de belirtmeliyiz.

### Veri Tiplerine Kısa Bir Bakış:
*   `INT`: Tam sayı (1, 2, 50, 1000)
*   `NVARCHAR(50)`: Maksimum 50 karakter alabilen, metin (Türkçe karakter dahil).

---

## 3. Primary Key ve Identity (Otomatik Artan ID) Kavramları

Bir tablodaki her bir kaydın (satırın) eşsiz olması gerekirse bunu **Primary Key (Birincil Anahtar)** ile sağlarız.  Tıpkı gerçek hayattaki TC Kimlik Numaramız gibidir. Okulda aynı isimden 5 tane "Ramazan" olabilir ancak herkesin TC Kimlik Numarası farklıdır. Veritabanı bunları karıştırmasın diye her öğrenciye benzersiz, sadece o kişiye özel bir numara verir. İşte bu benzersiz kimliğe Primary Key denir. 

Ayrıca bu eşsiz kimlik numarasının biz veri girdikçe otomatik olarak artmasını istiyorsak **IDENTITY** özelliğini kullanırız. Öğrenci kaydederken "Acaba bundan önceki öğrencinin ID'si kaç idi?" diye düşünmek istemeyiz. IDENTITY(1,1) komutu, veritabanına "Sen ilk kayda 1 ver, sonra gelenlere sırayla 2, 3, 4 diye otomatik olarak kendin artır" talimatını verir.

*   `IDENTITY(1,1)`: Başlangıç değeri 1 olsun, her yeni kayıtta 1'er 1'er artsın demektir.

Şimdi bu bilgilerle "Ogrenciler" adında bir tablo oluşturalım:

```sql
CREATE TABLE Ogrenciler (
    -- OgrenciID sütunu INT tipindedir. 
    -- PRIMARY KEY ile bu sütunun benzersiz olduğunu belirtiyoruz.
    -- IDENTITY(1,1) ile veritabanının bu numarayı otomatik vermesini sağlıyoruz.
    OgrenciID INT PRIMARY KEY IDENTITY(1,1),
    Ad NVARCHAR(50) ,   
    Soyad NVARCHAR(50),
    Bolum NVARCHAR(50)
);
```

---

## 4. Veri Yazma / Ekleme (INSERT INTO)

Tablomuzu oluşturduk. Şimdi içine veri yazma vakti. Veri eklemek için `INSERT INTO` komutunu kullanırız. 

> 💡 **Önemli İpucu:** Tablomuzu oluştururken `OgrenciID` sütununa `IDENTITY` özelliği vermiştik. Bu yüzden veri eklerken `OgrenciID` sütununa **biz değer göndermeyiz**. SQL Server bu numarayı kendisi atar!

```sql
-- Tek bir kayıt (satır) ekleme
INSERT INTO Ogrenciler (Ad, Soyad, Bolum)
VALUES ('Ramazan', 'Kansu','BP');

-- Aynı anda birden fazla kayıt ekleme
INSERT INTO Ogrenciler (Ad, Soyad, Bolum)
VALUES 
('Ayşe', 'Kaya','BP'),
('Zeynep','Koyun','BP'),
('Burak','Çetin','BP')
```

*Verileri okumak istersen `SELECT * FROM Ogrenciler;` komutunu çalıştırarak eklenen verileri ve otomatik oluşan ID'leri görebilirsin.*

<img width="299" height="143" alt="image" src="https://github.com/user-attachments/assets/f3e9e485-3e89-40d0-a10a-000edd38c672" />
---

## 5. Veri Güncelleme (UPDATE)

Tablodaki mevcut bir veriyi değiştirmek istediğimizde `UPDATE` komutunu kullanırız. 

> ⚠️ **KRİTİK UYARI:** `UPDATE` işlemi yaparken **KESİNLİKLE** `WHERE` koşulu kullanmalısın. Eğer `WHERE` koşulunu yazmayı unutursan, tablodaki **TÜM KAYITLAR** güncellenir!

### Örnek 1: Belirli bir kişiyi ID'sine göre güncelleme (En güvenli yol)
Ramazan'ın bölümünü değiştirelim, Ramazan'ın `OgrenciID`'sinin 1 olduğunu varsayıyoruz (Çünkü ilk onu ekledik).

```sql
UPDATE Ogrenciler
SET Bolum = 'Sivil Havacılık'
WHERE OgrenciID = 1;
```
<img width="352" height="137" alt="image" src="https://github.com/user-attachments/assets/275d36e5-4acd-4dc0-8dfa-9c81b0bf6f86" />

### Örnek 2: Koşula uyan birden fazla kaydı güncelleme
Bölümü 'BP' olan herkesin adını değiştirelim.

```sql
UPDATE Ogrenciler
SET Ad = 'TEST'
WHERE Bolum = 'BP';
```
<img width="382" height="131" alt="image" src="https://github.com/user-attachments/assets/fb3e005e-c08d-4766-bbd8-997ade4d4cd9" />

---

## Özet Akış

1. `CREATE DATABASE` ile Veri Tabanı yarat.
2. `CREATE TABLE` ile tabloyu ve sütunları ekle (Tanımla).
3. Tabloyu tanımlarken benzersizlik için `PRIMARY KEY`, otomatik numara için `IDENTITY` kullan.
4. `INSERT INTO` ile yeni satırlar ekle.
5. `UPDATE` ve `WHERE` kombinasyonu ile sadece istediğin kayıtları güvenle değiştir.
