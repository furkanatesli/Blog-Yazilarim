---
Başlık: Access’den C# ile Rastgele Veri Çekme
Paylaşım Tarihi: 14.02.2014
Etiketler: Access, Access random data extraction, access rastgele veri çekme
---

## Access’den C# ile Rastgele Veri Çekme

Veritabanı olarak Access kullandığım bir projede rastgele bir tane sözcük seçmem gerekiyordu.Ama Access’de kullanabildiğim ve her çalıştığında farklı değer döndüren sorgu C# da sürekli aynı değeri döndürüyordu.

Sorgu:

````access
select top 1 * from Tablo Adi order by rnd(ID)
````
Küçük bir araştırma sonucunda sorguda ufak değişiklikler yaptım:
````access
select top 1 * from Tablo Adi order by rnd(-ID*time())
````

Sorgunun son haliyle program istediğim bir şekilde çalıştı. Düştüğüm durumla karşılaşan olursa diye paylaşmak istedim umarım faydası olmuştur.
