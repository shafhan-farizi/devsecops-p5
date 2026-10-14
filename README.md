# Aplikasi Sistem Manajemen Parkir (Parking Management System)

Aplikasi ini adalah **Sistem Manajemen Parkir** berbasis web yang dirancang untuk mengelola pencatatan kendaraan masuk dan keluar, perhitungan tarif parkir secara otomatis, serta manajemen kapasitas slot parkir yang tersedia. Proyek ini merupakan pengembangan dari tugas proyek 2 tahun lalu yang kini diintegrasikan ke dalam pipeline DevSecOps untuk memenuhi tugas analisis keamanan kode.

## 1. Nama Aplikasi & Fungsi
* **Nama Aplikasi:** Parking Management System
* **Fungsi Utama:** 
  * Pencatatan data kendaraan masuk (cetak tiket parkir, input nomor polisi, dan jenis kendaraan).
  * Pencatatan kendaraan keluar beserta perhitungan tarif otomatis berdasarkan durasi parkir.
  * Monitoring kapasitas slot parkir (roda dua dan roda empat) secara *real-time*.
  * Pembuatan laporan pendapatan harian/bulanan dan riwayat transaksi parkir.

## 2. Bahasa & Framework yang Digunakan
* **Bahasa Pemrograman:** PHP
* **Framework Backend:** Laravel
* **Database:** MySQL

## 3. Cara Menjalankan Aplikasi
Untuk menjalankan aplikasi sistem parkir ini di lingkungan lokal Anda, ikuti langkah-langkah berikut:

1. **Clone Repository:**
   ```bash
   git clone https://github.com/shafhan-farizi/devsecops-p5.git
   cd devsecops-p5
   ```

2. **Install Dependencies:**
   ```bash
   composer install
   ```

3. **Konfigurasi Environment:**
   Salin file `.env.example` menjadi `.env` dan sesuaikan konfigurasi database lokal Anda.
   ```bash
   cp .env.example .env
   ```

4. **Generate Application Key:**
   ```bash
   php artisan key:generate
   ```

5. **Jalankan Migrasi & Seed Database:**
   *(Opsional jika proyek lama Anda menggunakan seeder untuk data awal tarif/admin)*
   ```bash
   php artisan migrate --seed
   ```

6. **Jalankan Server Lokal:**
   ```bash
   php artisan serve
   ```
   Aplikasi dapat diakses melalui browser di alamat `http://127.0.0.1:8000`.

## 4. Overview Pipeline CI/CD (DevSecOps)
Repository ini mengimplementasikan pipeline otomatis berbasis **GitHub Actions** yang terpicu setiap kali ada aktivitas `push` atau `pull_request` ke branch `main`. Pipeline dirancang dengan pendekatan DevSecOps untuk memastikan kode aplikasi parkir ini aman sebelum masuk ke tahap rilis.

Alur kerja pipeline terdiri dari 2 tahapan (*Jobs*) utama:
* **Job Build (CI & Unit Testing):**
  * Melakukan checkout kode sumber aplikasi parkir dari repository.
  * Mengonfigurasi lingkungan PHP dan ekstensi database (`sqlite`) di *runner* `ubuntu-latest`.
  * Menginstal seluruh dependensi framework menggunakan Composer.
  * Menyiapkan file konfigurasi lingkungan tiruan menggunakan SQLite `:memory:` agar proses pengujian tidak membutuhkan database MySQL asli.
  * Menjalankan Automated Unit Testing menggunakan PHPUnit untuk memastikan logika perhitungan tarif dan validasi input parkir berfungsi normal.
* **Job SAST Scan (Security Scanning):**
  * Berjalan secara sekuensial setelah *Job Build* berhasil dinyatakan lulus (`needs: build`).
  * Menggunakan alat **Semgrep** untuk melakukan *Static Application Security Testing* (SAST).
  * Memindai seluruh baris kode aplikasi berdasarkan aturan standar keamanan internasional (*OWASP Top 10* dan *PHP Security Audit*).
  * Mengidentifikasi celah keamanan (seperti fungsi enkripsi usang, *SQL injection* pada query pencarian plat nomor, atau *hardcoded credentials*) untuk dilaporkan dalam analisis Quality Gate.

