# Laporan Proyek Machine Learning - Nama Anda

## Project Overview

Sistem rekomendasi berperan penting dalam meningkatkan pengalaman pengguna, terutama di platform berbasis konten seperti toko buku online atau aplikasi pembaca digital. Dalam proyek ini, kami membangun sistem rekomendasi buku yang dipersonalisasi berdasarkan riwayat interaksi pengguna dan informasi konten buku.

Masalah ini penting karena sistem rekomendasi yang baik dapat meningkatkan keterlibatan pengguna, memperluas jangkauan bacaan mereka, serta mendukung penjualan dan retensi pengguna.

**Referensi**:
- Ricci, F., Rokach, L., & Shapira, B. (2015). *Recommender Systems Handbook*. Springer.
- Resnick, P., & Varian, H. R. (1997). *Recommender systems*. Communications of the ACM, 40(3), 56–58.

## Business Understanding

### Problem Statements

- Bagaimana merekomendasikan buku yang relevan dan menarik bagi setiap pengguna?
- Bagaimana membangun sistem yang memanfaatkan informasi buku dan riwayat pembacaan pengguna?

### Goals

- Menghasilkan daftar Top-N rekomendasi buku yang dipersonalisasi.
- Meningkatkan relevansi rekomendasi melalui pendekatan gabungan: konten dan kolaboratif.

### Solution Approach

Untuk mencapai tujuan di atas, kami mengusulkan dua pendekatan sistem rekomendasi:

1. **Content-Based Filtering**  
   Rekomendasi berdasarkan konten dari fitur buku seperti judul.

2. **Collaborative Filtering**  
   Menggunakan data interaksi antar pengguna untuk menemukan pola kesamaan dan memberikan rekomendasi.

## Data Understanding

Dataset yang digunakan diunduh dari Kaggle dan terdiri dari tiga file utama:
- Books.csv: berisi metadata buku
- Ratings.csv: berisi rating yang diberikan pengguna terhadap buku
- Users.csv: berisi informasi pengguna

Sumber dataset:  
[Kaggle - Book Recomendation Dataset](https://www.kaggle.com/datasets/arashnic/book-recommendation-dataset)

### Fitur-Fitur dalam Dataset:

- `user_id`: ID unik pengguna
- `Book-Title`: Judul buku
- `Book-Author`: Penulis buku
- `Book-Rating`: Rating buku
- `Age` : Usia pengguna

### Exploratory Data Analysis (EDA)

Beberapa temuan awal:
- Mayoritas pengguna hanya memberi sedikit rating (banyak data sparsity)
- Banyak buku yang hanya dirating sekali atau dua kali
- Penulis populer (seperti J.K. Rowling) cenderung memiliki rating lebih banyak

## Data Preparation

Langkah-langkah yang kami lakukan:

1. **Data Cleaning**  
   - Membersihkan data dari duplikat dan nilai-nilai yang tidak valid seperti umur ekstrem atau rating nol
   - Membuang kolom yang tidak relevan seperti URL gambar.

2. **Data Filtering**  
   - Mengambil subset dari data sebanyak 30.000 baris untuk mempercepat proses training dan menghindari beban komputasi berlebih.

3. **Merge Data**  
   - Menggabungkan dataset ratings, books, dan users menjadi satu dataframe utama (full_data) dan membuang kolom yang tidak diperlukan untuk fokus pada fitur penting

## Modeling

Kami membangun dua jenis sistem rekomendasi:

### 1. Content-Based Filtering

- Menggunakan TF-IDF vectorizer untuk fitur konten dari judul
- Menghitung similarity antar buku menggunakan cosine similarity
- Memberikan rekomendasi berdasarkan judul buku

**Output**:  
Top-5 rekomendasi buku berdasarkan konten yang mirip dengan judul buku

### 2. Collaborative Filtering (Matrix Factorization)

- Menggunakan teknik Singular Value Decomposition (SVD) dari `Surprise` library
- Mengestimasi rating yang belum diberikan pengguna
- Memberikan rekomendasi berdasarkan prediksi rating tertinggi

**Output**:  
Top-5 buku dengan estimasi rating tertinggi untuk setiap pengguna.

### Perbandingan Kedua Pendekatan

| Pendekatan             | Kelebihan                                  | Kekurangan                                  |
|------------------------|--------------------------------------------|---------------------------------------------|
| Content-Based          | Tidak memerlukan banyak data pengguna lain | Cenderung memberi rekomendasi sempit        |
| Collaborative Filtering| Bisa menangkap preferensi yang lebih luas  | Cold-start problem jika pengguna/item baru  |

## Evaluation
### Evaluation Model Content Based Filtering
Model Content Based Filtering dievaluasi seberapa presisi hasil terhadap input yang diberikan. Evaluasi ini mengukur seberapa banyak hasil rekomendasi yang mengandung kata kunci dari input judul (misalnya: "Baby").

**Rumus:**

$$
\text{Precision} = \frac{\text{Jumlah judul yang mengandung kata kunci}}{\text{Jumlah total rekomendasi}}
$$

**Contoh:**
- Input Judul: **"Baby"**
- Hasil Rekomendasi:
  1. He's My Baby Now ✅
  2. Baby ✅
  3. One for My Baby : A Novel ✅
  4. Baby Elephant (Baby) ✅
  5. Baby, Oh Baby! (Time of Your Life) ✅

**Hasil:**

| Metrik           | Nilai    |
|------------------|----------|
| Precision        | 1.00     |
| Total Rekomendasi| 5        |
| Match Judul      | 5        |

### Evaluation Model Collaborative Filtering
Model Collaborative Filtering dievaluasi menggunakan dua metrik regresi umum: **Mean Squared Error (MSE)** dan **Root Mean Squared Error (RMSE)**.  
Keduanya digunakan untuk mengukur sejauh mana prediksi model menyimpang dari nilai rating sebenarnya.

### Rumus

- **MSE (Mean Squared Error)**  
$$
\text{MSE} = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2
$$

MSE mengukur rata-rata selisih kuadrat antara rating aktual dan prediksi.

- **RMSE (Root Mean Squared Error)**  
$$
\text{RMSE} = \sqrt{\text{MSE}}
$$

RMSE lebih mudah diinterpretasikan karena satuannya sama dengan rating sebenarnya.

### Hasil Evaluasi Model Collaborative Filtering
- MSE: 3.7151
- RMSE: 1.9275
