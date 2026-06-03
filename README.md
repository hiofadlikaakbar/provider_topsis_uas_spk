# TOPSIS Provider

Sistem Pendukung Keputusan Pemilihan Provider Internet Terbaik menggunakan metode **TOPSIS (Technique for Order Preference by Similarity to Ideal Solution)**.

## Deskripsi

TOPSIS Provider adalah aplikasi web yang membantu pengguna menentukan provider internet terbaik berdasarkan berbagai kriteria penilaian secara objektif dan terstruktur. Sistem ini menerapkan metode **TOPSIS** untuk menghasilkan peringkat provider berdasarkan bobot dan nilai yang diberikan pada setiap kriteria.

Aplikasi dirancang untuk mengurangi bias subjektif dalam pengambilan keputusan dengan memanfaatkan perhitungan matematis yang transparan dan dapat dipertanggungjawabkan.

---

## Teknologi yang Digunakan

* Nuxt 4 — Framework berbasis Vue.js untuk pengembangan frontend modern.
* Tailwind CSS 4 — Framework utility-first untuk desain antarmuka yang responsif.
* Supabase — Backend-as-a-Service berbasis PostgreSQL untuk penyimpanan dan manajemen data.
* TypeScript — Menyediakan type safety dan meningkatkan kualitas kode.

---

## Fitur Utama

### 1. Manajemen Kriteria

Pengguna dapat:

* Menambah kriteria penilaian
* Mengubah data kriteria
* Menghapus kriteria
* Menentukan bobot setiap kriteria
* Menentukan jenis kriteria (Benefit atau Cost)

> Total bobot seluruh kriteria harus mencapai 100%.

### 2. Manajemen Provider

Pengguna dapat mengelola alternatif provider yang akan dibandingkan, meliputi:

* Menambah provider
* Mengedit provider
* Menghapus provider

### 3. Matriks Penilaian

Sistem menyediakan tabel matriks penilaian yang memudahkan pengguna untuk:

* Mengisi nilai setiap provider terhadap setiap kriteria
* Menyimpan data penilaian ke database Supabase
* Mengelola data penilaian secara terpusat

### 4. Perhitungan TOPSIS Otomatis

Sistem menghitung seluruh tahapan metode TOPSIS secara otomatis, meliputi:

1. Normalisasi matriks keputusan
2. Pembobotan matriks ternormalisasi
3. Penentuan solusi ideal positif (A+)
4. Penentuan solusi ideal negatif (A-)
5. Perhitungan jarak terhadap solusi ideal positif (D+)
6. Perhitungan jarak terhadap solusi ideal negatif (D-)
7. Perhitungan nilai preferensi (V)
8. Penentuan ranking alternatif

### 5. Hasil dan Ranking

Hasil perhitungan ditampilkan dalam bentuk:

* Podium 3 provider terbaik
* Tabel ranking lengkap beserta skor preferensi
* Tabel detail proses perhitungan TOPSIS
* Informasi solusi ideal positif dan negatif

### 6. Dashboard

Dashboard menyediakan ringkasan informasi sistem seperti:

* Jumlah kriteria
* Jumlah provider
* Jumlah data penilaian
* Alur penggunaan aplikasi
* Statistik data yang tersimpan

---

## Alur Penggunaan

```text
Input Kriteria dan Bobot
          ↓
     Input Provider
          ↓
      Isi Penilaian
          ↓
  Proses Perhitungan TOPSIS
          ↓
      Hasil dan Ranking
```

---

## Metode TOPSIS

TOPSIS (Technique for Order Preference by Similarity to Ideal Solution) merupakan metode pengambilan keputusan multikriteria yang menentukan alternatif terbaik berdasarkan kedekatannya dengan solusi ideal positif dan jauhnya dari solusi ideal negatif.

Tahapan metode yang diterapkan pada sistem ini meliputi:

1. Penyusunan matriks keputusan
2. Normalisasi matriks keputusan
3. Pembobotan matriks ternormalisasi
4. Penentuan solusi ideal positif dan negatif
5. Perhitungan jarak setiap alternatif terhadap solusi ideal
6. Perhitungan nilai preferensi
7. Penyusunan ranking alternatif

Alternatif dengan nilai preferensi tertinggi akan direkomendasikan sebagai pilihan terbaik.

---

## Tujuan

Aplikasi ini bertujuan membantu pengguna dalam memilih provider internet terbaik berdasarkan data dan kriteria yang relevan.

Dengan metode TOPSIS, proses pengambilan keputusan menjadi:

* Objektif
* Transparan
* Terukur
* Konsisten
* Mudah dipahami

---

## Pengembang

Aplikasi ini dikembangkan sebagai Sistem Pendukung Keputusan (SPK) berbasis web untuk membantu proses evaluasi dan pemilihan provider internet menggunakan metode TOPSIS secara efektif dan sistematis.
