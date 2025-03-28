# Optimasi Database dengan Partisi Tabel

## 1. Latar Belakang
Dalam pengelolaan database dengan jumlah data besar, performa query menjadi tantangan utama. Salah satu teknik yang dapat digunakan untuk meningkatkan efisiensi pencarian data adalah partisi tabel. Dengan membagi tabel besar menjadi beberapa bagian lebih kecil berdasarkan kriteria tertentu, sistem database dapat mempercepat proses pencarian dan pengolahan data, sehingga mengurangi beban kerja server.

## 2. Problem
- **Full Table Scan yang Lambat**: Tanpa partisi, database harus membaca seluruh tabel untuk menemukan data yang dicari, sehingga memperlambat waktu eksekusi query.
- **Kinerja Query Tidak Optimal**: Query yang melakukan filter berdasarkan rentang waktu berjalan lebih lambat karena harus memproses seluruh data dalam tabel besar.
- **Beban Server yang Tinggi**: Penyimpanan data dalam satu tabel besar meningkatkan penggunaan sumber daya server, sehingga memperlambat kinerja keseluruhan sistem.
  
## 3. Solusi atau Skenario Aktivitas
1. Redesaian tabel tr_penjualan, menambahkan partisi pada tabel tersebut. Sehingga ada tabel baru tr_penjualan_partisi
```sql
CREATE TABLE tr_penjualan_partisi (
tgl_transaksi DATETIME
DEFAULT NULL, kode_cabang VARCHAR(10)
DEFAULT NULL, kode_kasir VARCHAR(10)
DEFAULT NULL, kode_item VARCHAR(7)
DEFAULT NULL,kode_produk VARCHAR(12)
DEFAULT NULL, jumlah_pembelian INT(11)
DEFAULT NULL, nama_kasir VARCHAR(40)
DEFAULT NULL, harga INT(6) DEFAULT NULL
)
 PARTITION BY RANGE
(YEAR(tgl_transaksi)) (
PARTITION p0 VALUES LESS THAN (2008),
PARTITION p1 VALUES LESS THAN (2009),
PARTITION p2 VALUES LESS THAN (2010),
PARTITION p3 VALUES LESS THAN (2011),
PARTITION p4 VALUES LESS THAN (2012),
PARTITION p5 VALUES LESS THAN (2013),
PARTITION p6 VALUES LESS THAN (2014),
PARTITION p7 VALUES LESS THAN (2015)
);
```
2. Mengisikan tabel tr_penjualan_partisi.
```sql
--2008--
INSERT INTO tr_penjualan_partisi (tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga)
SELECT tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga_produk AS harga
FROM tr_penjualan
WHERE YEAR(tgl_transaksi) = 2008;

--2009--
INSERT INTO tr_penjualan_partisi (tgl_transaksi,
kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga)
SELECT DATE_ADD(tgl_transaksi, INTERVAL 1 YEAR), kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga_produk as harga FROM tr_penjualan
  WHERE YEAR(tgl_transaksi) = 2008;

--2010--
INSERT INTO tr_penjualan_partisi (tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga)
SELECT DATE_ADD(tgl_transaksi, INTERVAL 2 YEAR), kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga_produk as harga FROM tr_penjualan
WHERE YEAR(tgl_transaksi) = 2008;

--2011--
INSERT INTO tr_penjualan_partisi (tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga)
SELECT DATE_ADD(tgl_transaksi, INTERVAL 3 YEAR), kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga_produk as harga FROM tr_penjualan
 WHERE YEAR(tgl_transaksi) = 2008;
```
3. Mengisikan tabel tr_penjualan_partisi dengan kapasitas laptop
```sql
SELECT TABLE_NAME, PARTITION_NAME, TABLE_ROWS FROM INFORMATION_SCHEMA.PARTITIONS
 WHERE TABLE_NAME = 'tr_penjualan_partisi';
```
4. Susunan record sesuai partisi akan ditampilkan
```sql
CREATE TABLE
tr_penjualan_raw
( tgl_transaksi DATETIME DEFAULT NULL,
kode_cabang VARCHAR(10) DEFAULT NULL,
kode_kasir VARCHAR(10) DEFAULT NULL,
kode_item VARCHAR(7) DEFAULT NULL,
kode_produk VARCHAR(12) DEFAULT NULL,
jumlah_pembelian INT(11) DEFAULT NULL,
nama_kasir VARCHAR(40) DEFAULT NULL,
harga INT(6) DEFAULT NULL
);
```
5. Buat tabel tr_penjualan_raw yang isinya sama persis dengan tabel tr_penjualan_partisi
```sql
INSERT INTO tr_penjualan_raw
(tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga)
SELECT
tgl_transaksi, kode_cabang, kode_kasir, kode_item, kode_produk, jumlah_pembelian, nama_kasir, harga
 FROM tr_penjualan_partisi;
```
6. Pengujian kolom lain pada tabel non partisi dengan perulangan 10x
```sql
SELECT * FROM tr_penjualan_raw WHERE tgl_transaksi > DATE('2010-08-01') AND tgl_transaksi < DATE('2011-07-31');
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 15.486   |
| 2         | 15.358   |
| 3         | 15.464   |
| 4         | 15.508   |
| 5         | 15.508   |
| 6         | 15.313   |
| 7         | 17.193   |
| 8         | 15.413   |
| 9         | 15.279   |
| 10        | 15.122   |
**Rata-rata: 15.733 s**
7. Pengujian kolom lain pada tabel partisi dengan perulangan 10x
```sql
SELECT * FROM tr_penjualan_partisi WHERE tgl_transaksi > DATE('2010-08-01')
 AND tgl_transaksi < DATE('2011-07-31');
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 14.275   |
| 2         | 14.486   |
| 3         | 14.289   |
| 4         | 14.537   |
| 5         | 14.992   |
| 6         | 14.015   |
| 7         | 14.268   |
| 8         | 14.92    |
| 9         | 14.45    |
| 10        | 13.847   |
**Rata-rata: 14.308 s**

## 4. Pembahasan
Dalam percobaan ini, tabel tr_penjualan dioptimalkan dengan teknik RANGE PARTITIONING, di mana data dipartisi berdasarkan tahun transaksi. Setelah implementasi, uji coba menunjukkan bahwa query yang memanfaatkan kolom partisi memiliki waktu eksekusi lebih cepat dibandingkan dengan tabel tanpa partisi. Teknik ini terbukti efektif dalam meningkatkan performa database dengan mengurangi jumlah data yang perlu diproses dalam pencarian berbasis rentang waktu.

## 5. Kesimpulan
Berdasarkan hasil percobaan, waktu eksekusi query pada tabel partisi dengan tgl_transaksi menunjukkan rata-rata yang lebih rendah dibandingkan tabel tanpa partisi. Pada tabel tanpa partisi, rata-rata waktu eksekusi berkisar antara 15.122 hingga 17.193 ms, sedangkan pada tabel partisi, rata-rata waktu eksekusi lebih stabil dalam rentang 13.847 hingga 14.992 ms. Hal ini membuktikan bahwa partisi tabel mampu meningkatkan efisiensi pencarian data, terutama saat query dilakukan berdasarkan kolom yang digunakan sebagai dasar partisi. Dengan demikian, penggunaan partisi sangat efektif dalam mempercepat akses data pada skenario tertentu, terutama yang melibatkan pencarian berbasis rentang waktu.

## 6. Bukti Dukung Gambar
1. Redesaian tabel tr_penjualan, menambahkan partisi pada tabel tersebut. Sehingga ada tabel baru tr_penjualan_partisi
![1](https://github.com/user-attachments/assets/ce5b83a4-928c-4cea-9a5d-550dd4eba5fd)
2. Mengisikan tabel tr_penjualan_partisi.
![2](https://github.com/user-attachments/assets/4b7ab3c4-97ed-4b3f-9e89-2658d54e8af8)
3. Mengisikan tabel tr_penjualan_partisi dengan kapasitas laptop
![3](https://github.com/user-attachments/assets/3a075e41-e7e5-4698-b2f4-ffcdc99272d7)
4. Susunan record sesuai partisi akan ditampilkan
![4](https://github.com/user-attachments/assets/a7cfcc2a-13bf-4bb3-9c09-05bb66761b6d)
5. Buat tabel tr_penjualan_raw yang isinya sama persis dengan tabel tr_penjualan_partisi
![5](https://github.com/user-attachments/assets/52c77c97-8d27-49ff-ad18-f9fd92a3bca4)
6. Pengujian kolom lain pada tabel non partisi
![6](https://github.com/user-attachments/assets/3c04c99a-9381-4c0a-9dde-4f009851a659)
7. Pengujian kolom lain pada tabel partisi
![7](https://github.com/user-attachments/assets/fef07d0a-bbbd-4d2c-b324-a007b2ca1b59)

## 7. Sumber Referensi
- [Dokumentasi MySQL Partisi Tabel](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html)  
- [Praktikum Sistem Manajemen Basis Data](https://drive.google.com/file/d/1owdQasYnWgnII95wxeOau5ixu9UYlGoS/view?usp=sharing)
