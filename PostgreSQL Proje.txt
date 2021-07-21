/* Merhaba... Ben Misli Çelik.
Sizlerle internetten bulduğum bir veri seti ile çalışma yapmak istedim.
Umarım Beğenirsiniz... */

/* 1-) Tabloloarı birleştirip viewler ile saklayalım. Diğer işlemlerimizde kolaylık sağlamış oluruz. */

-- Join işlemlerinde il kısmını da ayrıca dahil edebilmek için view'den yardım alalım

CREATE VIEW ililce
AS
SELECT il.il_ad, ilce.ilce_ad, 
ilce.ilce_no, il.il_no FROM il 
JOIN ilce ON ilce.il_no = il.il_no;

-- Tabloları İNNER JOİN ile birleştirip ortak bir tabloda buluşturalım.

SELECT p.personel_no, p.ad, p.soyad, p.cinsiyet,
p.dogum_tarihi, i.ilce_ad, i.il_ad,
p.baslama_tarihi, b.birim_ad, u.unvan_ad,
p.calisma_saati, p.maas, p.prim
FROM personel p
JOIN ililce i ON p.dogum_yeri = i.ilce_no
JOIN birim b ON p.birim_no = b.birim_no
JOIN unvan u ON p.unvan_no = u.unvan_no;

-- Joinlerimizi de bir view olarak kayedelim.

CREATE VIEW sirket
AS
SELECT p.personel_no, p.ad, p.soyad, p.cinsiyet,
p.dogum_tarihi, i.ilce_ad, i.il_ad,
p.baslama_tarihi, b.birim_ad, u.unvan_ad,
p.calisma_saati, p.maas, p.prim
FROM personel p
JOIN ililce i ON p.dogum_yeri = i.ilce_no
JOIN birim b ON p.birim_no = b.birim_no
JOIN unvan u ON p.unvan_no = u.unvan_no;

/* 2-) Şirkette kaç Personel vardır? */

SELECT COUNT(*) FROM sirket;

/* 3-) Şirkette Ankara'da doğan kaç kişi vardır? */

SELECT COUNT(il_ad) FROM sirket
WHERE il_ad='ANKARA';

/* 4-) Şirkette hem Ankara'da ilçesi de Yenimahalle olan kaç kişi vardır? */

SELECT il_ad, COUNT(ilce_ad) FROM sirket
WHERE il_ad='ANKARA' AND ilce_ad='YENİMAHALLE'
GROUP BY il_ad;

/* 5-) Cinsiyete göre Ankara'da yaşayan kaç kişi var? */

SELECT il_ad, (SELECT COUNT(*) FROM sirket WHERE cinsiyet='K') AS Kadın, 
(SELECT COUNT(*) FROM sirket WHERE cinsiyet='E') AS Erkek
FROM sirket
GROUP BY il_ad;

/* 6-) Şirkette ki çalışanlar kaç yaşında */

SELECT ad, soyad, AGE(NOW(), dogum_tarihi) AS CalisanYasi FROM sirket;

/* 7-) Şirkette ki çalışanlar kaç yaşında işe başladılar? */

SELECT ad, soyad, AGE(NOW(), dogum_tarihi) AS KacYasindaBasladi FROM sirket;

/* 8-) Şirkette ki çalışanlar kaç yıldır çalışıyorlar */

SELECT ad, soyad, AGE(NOW(), baslama_tarihi) AS NeKadardirCalisiyor FROM sirket;

/* 9-) En Fazla Maaş tutarını alan Personel Kimdir? */

SELECT ad, soyad, (SELECT MAX(maas+prim) FROM sirket)
FROM sirket;

/* 10-) Mühendis ünvanına sahip ve birimi Kalite olan personel kimdir */

SELECT ad, soyad, unvan_ad, birim_ad, il_ad
FROM sirket
WHERE unvan_ad= 'MÜHENDİS' AND birim_ad='KALİTE';

/* 11-) Her İlde en çok personele sahip olan ilçeleri personal sayısına göre çoktan aza 
doğru sıralayan sorgu. */

SELECT il_ad, ilce_ad, count(*) AS PersonelSayısı FROM sirket
GROUP BY il_ad, ilce_ad
ORDER BY il_ad, ilce_ad;

/* 12-) isim kısaltmalarının kaç tane olduğunu gösteren kodu yazalım */

SELECT CONCAT(SUBSTRING(ad, 1, 1),'.',SUBSTRING(soyad, 1,1),'.') Kisaİsim,
COUNT(*) PersonelSayisi FROM sirket
GROUP BY SUBSTRING(ad, 1, 1),SUBSTRING(soyad, 1,1)
ORDER BY COUNT(*);

