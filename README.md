# Prediksi Hujan Hari Berikutnya di Kota Medan

## Deskripsi

Penelitian ini membandingkan algoritma K-Nearest Neighbors (KNN)
dan Decision Tree untuk memprediksi status hujan pada hari
berikutnya berdasarkan data cuaca historis Kota Medan.

## Tujuan Penelitian

Mengevaluasi dan membandingkan performa kedua algoritma
dengan menggunakan lima split temporal cross-validation.

## Dataset

- Sumber: Open-Meteo Historical Weather API
- Model data: ERA5-Land reanalysis
- Lokasi: Kota Medan, Indonesia
- Periode fitur: 1 Januari 2023 sampai 31 Desember 2025
- Target: status hujan pada hari berikutnya
- Definisi hujan: rain_sum > 0 mm

## Metodologi

1. Pengambilan dan pemeriksaan data
2. Preprocessing dan pembentukan target
3. Pelatihan model KNN
4. Pelatihan model Decision Tree
5. Evaluasi dengan TimeSeriesSplit, 5 split
6. Analisis metrik dan confusion matrix

## Struktur Repository

- `data/`: data mentah dan data siap model
- `notebooks/`: notebook eksperimen
- `reports/figures/`: visualisasi hasil
- `reports/tables/`: tabel hasil evaluasi
- `docs/`: catatan metodologi

## Hasil

Akan diisi setelah eksperimen selesai dijalankan dan diverifikasi.

## Cara Menjalankan

Buka notebook di folder `notebooks/` menggunakan
Google Colab atau Jupyter Notebook.

## Sumber Data

Open-Meteo Historical Weather API:
https://open-meteo.com/en/docs/historical-weather-api

Cantumkan atribusi sumber data sesuai ketentuan Open-Meteo.

## Anggota Kelompok

- Cristoval Pratama Siahaan (241401057)
- Nama anggota 2
- Nama anggota 3
- Nama anggota 4
