# Optimasi Performa Database dengan Index

## 1. Latar Belakang
Dalam sistem basis data, optimasi performa sangat penting untuk memastikan efisiensi dalam pengolahan dan pencarian data. Salah satu teknik yang dapat diterapkan adalah penggunaan indeks pada tabel database. Indeks dapat mempercepat proses pencarian data dengan mengurangi kebutuhan untuk melakukan full table scan dan meningkatkan performa query dalam database MySQL.

## 2. Problem
Beberapa tantangan utama yang dihadapi dalam pengelolaan database tanpa indeks adalah :
- Full Table Scan atau Query harus membaca seluruh tabel untuk menemukan data yang dicari.
- Kurangnya Optimasi dalam Join Query
- Referensial Integritas Tidak Terjamin, tidak adanya foreign key dapat menyebabkan data menjadi tidak konsisten.
- Performa Query yang Tidak Optimal.

## 3. Solusi atau Skenario Aktivitas
Berikut adalah langkah-langkah implementasi yang telah dilakukan :

### **A. Melakukan Query Tanpa Index**
Sebelum menambahkan index, dilakukan pencarian berdasarkan `first_name` menggunakan perintah berikut:

```sql
EXPLAIN SELECT * FROM employee WHERE first_name = 'Georgi';
```

Hasil `EXPLAIN` menunjukkan bahwa query menggunakan **full table scan**, yang berarti semua baris dalam tabel diperiksa satu per satu.

### **B. Menambahkan Index**
Untuk meningkatkan performa, ditambahkan index pada kolom `first_name` dan `last_name`:

```sql
ALTER TABLE employee ADD INDEX idx_full_name (first_name, last_name);
```

Setelah index ditambahkan, query yang sama dijalankan kembali menggunakan `EXPLAIN`. Hasilnya menunjukkan bahwa query kini menggunakan **index scan**, yang jauh lebih efisien dibandingkan full table scan.

### **C. Pengujian Sebelum dan Sesudah Menggunakan Index**
Untuk mengukur efektivitas index, dilakukan pengujian dengan menjalankan query sebanyak 10 kali, lalu mencatat waktu eksekusinya.

#### **Sebelum Menggunakan Index:**
```sql
SELECT * FROM employee WHERE first_name = 'Georgi' AND last_name = 'Bahr';
```
Hasil waktu eksekusi:
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 0.38     |
| 2         | 0.47     |
| 3         | 0.41     |
| 4         | 0.42     |
| 5         | 0.41     |
| 6         | 0.40     |
| 7         | 0.40     |
| 8         | 0.44     |
| 9         | 0.41     |
| 10        | 0.39     |
**Rata-rata: 0.371 s**

#### **Setelah Menggunakan Index:**
| Percobaan | Waktu (s) |
|-----------|----------|
| 1         | 0.04     |
| 2         | 0.00     |
| 3         | 0.00     |
| 4         | 0.00     |
| 5         | 0.00     |
| 6         | 0.00     |
| 7         | 0.00     |
| 8         | 0.00     |
| 9         | 0.00     |
| 10        | 0.00     |
**Rata-rata: 0.004 s**

### **D. Menambahkan Kolom Baru dan Mengupdate Data**
Selain pengujian index, dilakukan juga beberapa modifikasi tabel:
- **Menambahkan kolom `nama_departemen`** pada tabel `dept_manager` dan `dept_emp`, lalu mengupdate nilainya.
- **Menambahkan kolom `umur`** pada tabel `employee`, lalu menghitung umurnya berdasarkan tanggal lahir.

```sql
ALTER TABLE dept_manager ADD COLUMN nama_departemen VARCHAR(255);
UPDATE dept_manager dm 
JOIN department d ON dm.dept_no = d.dept_no 
SET dm.nama_departemen = d.dept_name;

ALTER TABLE employee ADD COLUMN umur INT;
UPDATE employee 
SET umur = TIMESTAMPDIFF(YEAR, birth_date, CURDATE());
```

### **E. Menambahkan Foreign Key Index**
Foreign key index ditambahkan untuk menjaga integritas referensial antara tabel `dept_emp` dan `dept_manager` dengan tabel `employee`:

```sql
ALTER TABLE dept_emp 
ADD CONSTRAINT fk_dept_emp FOREIGN KEY (emp_no) REFERENCES employee(emp_no);

ALTER TABLE dept_manager 
ADD CONSTRAINT fk_dept_manager FOREIGN KEY (emp_no) REFERENCES employee(emp_no);
```

### **F. Mencari Gaji Tertinggi dalam Departemen Tertentu**
Untuk menemukan karyawan dengan gaji tertinggi di departemen `d006`, digunakan query berikut:

```sql
SELECT e.first_name, e.last_name, s.amount AS salary
FROM employee e
JOIN salary s ON e.emp_no = s.emp_no
JOIN dept_emp de ON e.emp_no = de.emp_no
WHERE de.dept_no = 'd006'
AND s.amount = (
    SELECT MAX(s2.amount)
    FROM salary s2
    JOIN dept_emp de2 ON s2.emp_no = de2.emp_no
    WHERE de2.dept_no = 'd006'
);
```

Hasilnya menunjukkan nama karyawan dengan gaji tertinggi dalam departemen tersebut.

## 4. Pembahasan
Sebelum indeks diterapkan, query membutuhkan full table scan yang memperlambat pencarian data. Dengan menambahkan composite index pada kolom yang sering digunakan dalam filter serta foreign key untuk menjaga referensial integritas, MySQL dapat mengoptimalkan eksekusi query. Hasil uji coba menunjukkan bahwa waktu eksekusi query berkurang signifikan setelah indeks diterapkan, membuktikan bahwa indeks sangat efektif dalam meningkatkan efisiensi pencarian dan join query.

## 5. Kesimpulan
Penggunaan indeks pada database sangat berpengaruh terhadap kinerja query, terutama dalam mengurangi waktu eksekusi dan meningkatkan efisiensi pencarian data. Dengan menambahkan indeks, seperti composite index pada kolom first_name dan last_name, serta foreign key index, proses query menjadi lebih cepat dibandingkan dengan full table scan. Selain itu, melalui eksperimen pengujian waktu eksekusi sebelum dan sesudah indeks ditambahkan, terlihat bahwa rata-rata waktu eksekusi berkurang secara signifikan setelah indeks diterapkan. Implementasi indeks yang tepat sangat penting dalam optimasi database untuk menangani jumlah data yang besar secara lebih efisien.

## 6. Bukti Dukung Gambar
## 7. Sumber Referensi
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [Praktikum Sistem Manajemen Basis Data](https://drive.google.com/file/d/1PMlqZAO8x-HlT8XKu6N3QbCgqesdyzJf/view?usp=sharing)
