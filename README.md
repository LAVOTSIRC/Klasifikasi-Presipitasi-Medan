# Perbandingan KNN dan Decision Tree untuk

# Klasifikasi Presipitasi Harian di Kota Medan

## Deskripsi

Proyek tugas kelompok Machine Learning Fundamental ini
membandingkan K-Nearest Neighbors (KNN) dan Decision Tree
untuk klasifikasi kategori presipitasi harian berdasarkan
data historis Open-Meteo untuk grid cuaca dekat Kota Medan.

## Tujuan

Membandingkan performa dua algoritma klasifikasi
menggunakan lima fold cross-validation.

## Dataset

- Sumber: Open-Meteo Historical Weather API
- Model yang dipilih: ECMWF IFS
- Interval: harian (Daily)
- Periode analisis: 1 Januari 2023–31 Desember 2025
- Unit observasi: satu hari

## Fitur

- Suhu rata-rata 2 meter
- Kelembapan relatif rata-rata 2 meter
- Tutupan awan rata-rata
- Tekanan permukaan laut rata-rata
- Kecepatan angin rata-rata 10 meter

## Target

- 0: precipitation_sum < 10 mm
- 1: precipitation_sum >= 10 mm

Kelas 0 tidak berarti tidak hujan. Kelas tersebut juga
dapat berisi hari dengan hujan ringan.

## Metodologi

1. Pemeriksaan dan pembersihan data
2. Pembentukan target klasifikasi
3. StandardScaler untuk KNN
4. Pelatihan KNN dan Decision Tree
5. Evaluasi StratifiedKFold 5-fold
6. Perbandingan metrik dan confusion matrix

## Hasil

Hasil aktual tersedia di reports/tables dan
visualisasi di reports/figures. Interpretasi hasil
dilakukan berdasarkan output notebook.

## Batasan

Data merupakan data historis berbasis model/grid,
bukan pengukuran langsung stasiun BMKG. Eksperimen
ini mengklasifikasikan kategori presipitasi pada hari
yang sama dan bukan prakiraan hujan untuk hari berikutnya.

## Menjalankan Proyek

Buka notebooks/01_klasifikasi_knn_vs_decision_tree.ipynb
menggunakan Jupyter Notebook atau JupyterLab.
Lihat requirements.txt untuk dependensi Python.

## Sumber Data

https://open-meteo.com/en/docs/historical-weather-api
