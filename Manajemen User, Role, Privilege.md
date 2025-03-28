# Manajemen User, Role, Privilege

## 1. Latar Belakang
Manajemen pengguna, peran (role), dan hak akses (privilege) dalam sistem manajemen basis data sangat penting untuk menjaga keamanan dan keteraturan dalam pengelolaan data. Dengan adanya sistem manajemen ini, administrator dapat mengontrol siapa yang dapat mengakses, mengubah, atau menghapus data dalam database. Praktikum ini bertujuan untuk memahami bagaimana membuat pengguna, memberikan hak akses, mengelola peran, serta melakukan monitoring aktivitas database dalam MySQL.

## 2. Problem
- **Kurangnya Pengelolaan Hak Akses**: Tanpa pengaturan role dan privilege, pengguna dapat memiliki akses berlebihan atau tidak memiliki akses yang cukup.
- **Keamanan Data yang Rentan**: Tanpa manajemen hak akses yang baik, data bisa diakses atau dimodifikasi oleh pihak yang tidak berwenang.
- **Kesulitan dalam Monitoring Aktivitas**: Tidak adanya pencatatan aktivitas pengguna dalam database dapat menyulitkan proses audit dan troubleshooting.
## 3. Solusi atau Skenario Aktivitas
1. Pembuatan username sebanyak jumlah kelompok.
```sql
PEMBUATAN USERNAME DAN PASSWORD :
CREATE USER 'elsa'@'localhost' IDENTIFIED BY '12345678';
CREATE USER 'puri'@'localhost' IDENTIFIED BY 'puri02';
CREATE USER 'irfan'@'localhost' IDENTIFIED BY 'irfan03';
CREATE USER 'rifky'@'localhost' IDENTIFIED BY 'rifky04';

MENAMPILKAN HASIL :
SELECT User, Host FROM mysql.user;
```
2. Penghapusan username terhadap user yang sudah dibuat.
```sql
DROP USER 'user_irfan'@'localhost';

MENAMPILKAN HASIL :
SELECT User, Host FROM mysql.user;
```
3. Membuat role dengan 'role_nama_anda_insert_select” → role_andi_select_insert'.
```sql
CREATE ROLE 'role_elsa_select_insert';
```
4. Memberikan privilege select, insert kedalam role diatas.
```sql
GRANT SELECT, INSERT ON kelompok_9_muhammadirfannurilanwar.* TO 'role_irfan_select_insert';

MENAMPILKAN HASIL :
SHOW GRANTS FOR 'role_irfan_select_insert';
```
5. Membuat role dengan “role_nama_anda_create_drop” → role_andi_create_drop.
```sql
CREATE ROLE 'role_uul_create_drop';

MENAMPILKAN HASIL :
SHOW GRANTS FOR 'role_uul_create_drop';
```
6. Memberikan privilege create, drop kedalam role diatas.
```sql
GRANT CREATE, DROP ON database_example.* TO 'role_elsa_create_drop';

MENAMPILKAN HASIL :
SELECT User, Host FROM mysql.user;
```
7. Memberikan 2 user kedalam masing-masing role diatas.
```sql
GRANT 'role_irfan_select_insert' TO 'rifky'@'localhost', 'elsa'@'localhost';

GRANT 'role_irfan_select_insert' TO 'rifky'@'localhost', 'elsa'@'localhost';
MELIHAT HASIL :
SHOW DATABASES;
```
 8. Pengujian sebelum dan sesudah user diberikan role.

SEBELUM
```sql
SHOW DATABASES;
```
SESUDAH
```sql
SET ROLE ALL;
```
9. Melepas role dari user diatas. Sehingga user menjadi tidak memiliki role.
```sql
mysql> drop role 'role_rifky_select_insert';

mysql> drop role 'role_rifky_create_drop';
```
## 4. Pembahasan
Pembuatan beberapa pengguna dan pemberian peran dengan hak akses spesifik seperti SELECT, INSERT, CREATE, dan DROP. Uji coba dilakukan sebelum dan sesudah pemberian role untuk melihat perubahan aksesibilitas pengguna. Selain itu, monitoring aktivitas database diterapkan untuk mencatat transaksi yang dilakukan oleh user. Hasilnya menunjukkan bahwa dengan pengelolaan user, role, dan privilege yang baik, sistem basis data dapat lebih aman, terstruktur, dan mudah dikelola.

## 5. Kesimpulan
Kesimpulan dari kegiatan praktikum ini adalah bahwa manajemen pengguna, peran (role), dan hak akses (privilege) dalam MySQL sangat penting untuk mengatur kontrol akses dalam sistem basis data. Melalui serangkaian perintah SQL, kelompok berhasil membuat pengguna baru, memberikan peran dengan hak akses tertentu, menguji efektivitas peran, serta melakukan monitoring terhadap aktivitas database. Praktik ini menunjukkan bagaimana administrator dapat mengelola pengguna dengan lebih efisien menggunakan peran, serta bagaimana hak akses dapat diberikan, diuji, dan dicabut untuk memastikan keamanan serta keteraturan dalam pengelolaan basis data. 

## 6. Bukti Dukung Gambar
1. Pembuatan username sebanyak jumlah kelompok.

![1](https://github.com/user-attachments/assets/ffdaf7dd-7515-4066-88d3-13f0b61e99b5)

2. Penghapusan username terhadap user yang sudah dibuat.

![2](https://github.com/user-attachments/assets/38803886-95c4-4f5f-bec0-788ff863f67a)

3. Membuat role dengan 'role_nama_anda_insert_select” → role_andi_select_insert'.

![3](https://github.com/user-attachments/assets/8a77b94e-5a43-4ae3-aa1c-a9275944132f)

5. Membuat role dengan “role_nama_anda_create_drop” → role_andi_create_drop.

![5](https://github.com/user-attachments/assets/f946aa48-7ecf-4d33-84b6-c3b7cd8cbcbf)

6. Memberikan privilege create, drop kedalam role diatas.

![6](https://github.com/user-attachments/assets/4544a100-95a8-4ae0-8826-c97959b1f8ac)

7. Memberikan 2 user kedalam masing-masing role diatas.

![7](https://github.com/user-attachments/assets/54be9241-6e6f-4891-8b6a-7066b84ec97d)

 8. Pengujian sebelum dan sesudah user diberikan role.

SEBELUM

![8](https://github.com/user-attachments/assets/2924c24d-f0ec-4611-8898-39402cfcbb4e)

SESUDAH

![8](https://github.com/user-attachments/assets/74ae7c29-f5a2-4920-943e-1fb67052d386)

9. Melepas role dari user diatas. Sehingga user menjadi tidak memiliki role.

![9](https://github.com/user-attachments/assets/31ab4461-ccc0-4114-a157-3866cf74f840)

## 7. Sumber Referensi
- [Dokumentasi MySQL Partisi Tabel](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html)  
- [Praktikum Sistem Manajemen Basis Data](https://drive.google.com/file/d/1owdQasYnWgnII95wxeOau5ixu9UYlGoS/view?usp=sharing)
