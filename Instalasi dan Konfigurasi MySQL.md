# Instalasi dan Konfigurasi MySQL

## 1. Latar Belakang
MySQL merupakan salah satu sistem manajemen basis data relasional (RDBMS) yang paling banyak digunakan. Dalam proses implementasi MySQL, instalasi dan konfigurasi yang tepat sangat penting untuk memastikan kinerja optimal serta keamanan sistem database. Pengaturan yang kurang optimal dapat menyebabkan berbagai permasalahan, seperti lambatnya eksekusi query, penggunaan sumber daya yang tidak efisien, dan potensi celah keamanan. Oleh karena itu, diperlukan pemahaman terkait cara menginstal serta mengonfigurasi MySQL agar sesuai dengan kebutuhan sistem yang akan digunakan.

## 2. Problem
Dalam proses instalasi dan konfigurasi MySQL, beberapa permasalahan yang sering ditemui antara lain:

- Kesalahan dalam mengatur akses pengguna atau hak istimewa (privileges).
- Pengaturan port MySQL dari 3306 menjadi 3309 untuk menghindari konflik layanan dan meningkatkan keamanan.
- Parameter innodb_buffer_pool_size yang terlalu kecil dapat menyebabkan eksekusi query menjadi lambat.
- Hak akses yang terlalu luas untuk pengguna tertentu bisa menyebabkan kebocoran atau kerusakan data.

## 3. Solusi atau Skenario Aktivitas
Berikut langkah-langkah untuk menginstal dan mengonfigurasi MySQL secara optimal:

### A. Instalasi MySQL
1. Buka browser dan cari "Download MySQL".
2. Pilih "MySQL Community (GPL) Downloads".
3. Unduh "MySQL Installer for Windows".
4. Pilih opsi **Server Only** dan klik **Next**.
5. Klik **Execute** untuk memulai instalasi.
6. Setelah instalasi selesai, lanjutkan dengan konfigurasi awal.

### B. Mengubah Port MySQL dari 3306 ke 3309
1. Masuk ke MySQL menggunakan **Command Prompt**.
2. Cek port saat ini dengan perintah:
   ```sql
   SHOW VARIABLES LIKE 'port';
   ```
3. Cari file `my.ini` menggunakan `%PROGRAMDATA%`.
4. Buka file `my.ini` dan ubah nilai `port` dari `3306` ke `3309`.
5. Stop MySQL sebelum menyimpan perubahan.
6. Restart MySQL dan cek kembali port dengan perintah yang sama.

### C. Mengubah `innodb_buffer_pool_size`
1. Buka file `my.ini`.
2. Cari parameter `innodb_buffer_pool_size`.
3. Ubah dari `16M` menjadi **25% dari total RAM** yang tersedia.
4. Restart MySQL untuk menerapkan perubahan.
5. Cek perubahan dengan perintah:
   ```sql
   SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
   ```
   
### D. Mengubah Password Root MySQL
1. Masuk ke MySQL sebagai root dengan:
   ```sh
   mysql -u root -p
   ```
2. Ubah password dengan perintah:
   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED BY 'password_baru';
   ```
3. Verifikasi perubahan dengan login ulang.

### E. Membuat Database Baru
1. Buat database dengan format `kelompok_AB_nama_mhs`:
   ```sql
   CREATE DATABASE kelompok_A1_elsa;
   ```
2. Cek apakah database berhasil dibuat:
   ```sql
   SHOW DATABASES;
   ```
   
## 4. Pembahasan
Pembahasan ini mencakup langkah-langkah instalasi dan konfigurasi MySQL untuk memastikan sistem database berfungsi dengan optimal. Proses instalasi dimulai dengan mengunduh MySQL. Selain itu, pengelolaan database, mengecek konfigurasi MySQL, dan memastikan parameter yang diatur sudah berjalan sesuai kebutuhan sistem.

## 5. Kesimpulan
 instalasi dan konfigurasi MySQL yang tepat sangat penting untuk memastikan performa dan keamanan database. Dengan mengubah port default, mengatur password root, serta mengoptimalkan innodb_buffer_pool_size, MySQL dapat berjalan lebih efisien. Pengelolaan database melalui Command Prompt juga memberikan fleksibilitas dalam administrasi sistem.

## 6. Bukti Dukung Gambar
Instalasi
1) Jika instal telah selesai MySQL otomatis membuka lalu pilih saja server only dan klik next.
   
   ![image](https://github.com/user-attachments/assets/d5c3925d-7e10-4426-8b83-76adec3c3889)
2) Lalu klik saja Execute untuk melanjutkan menginstall.

   ![image](https://github.com/user-attachments/assets/edc4ffa8-9837-4e13-8282-cd925e335d5e)

Konfigurasi

1)	port dari default 3306 menjadi 3309
   
   ![1](https://github.com/user-attachments/assets/0e14f3dc-6278-4364-a231-db5118317f76)
2)	innodb_buffer_pool_size dr default 16M (menjadi 25% dari RAM )

   ![image](https://github.com/user-attachments/assets/145598d3-430e-4d8b-9968-eb27ce423bca)
3) Lakukan perubahan terhadap password root.

   ![image](https://github.com/user-attachments/assets/f802c8d0-41e2-40af-b163-7c81f84a995a)
4. Buat database dengan nama: kelompok_AB_nama_mhs

   ![image](https://github.com/user-attachments/assets/abebee92-394c-498d-8060-eb8253d9eb1b)

## 7. Sumber Referensi
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [Panduan Praktikum Sistem Manajemen Basis Data](https://drive.google.com/file/d/1E1SBJXj0sZxMpt6FOjliDfrdSAc26y47/view?usp=sharing)
