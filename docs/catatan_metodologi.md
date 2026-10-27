
# Catatan Metodologi Penelitian

## 1. Gambaran Umum

Proyek ini merupakan eksperimen supervised learning untuk membandingkan algoritma K-Nearest Neighbors (KNN) dan Decision Tree dalam mengklasifikasikan kategori presipitasi harian berdasarkan data cuaca historis di sekitar Kota Medan.

Penelitian menggunakan lima variabel meteorologis sebagai fitur masukan dan jumlah presipitasi harian sebagai dasar pembentukan target klasifikasi. Performa kedua algoritma dibandingkan menggunakan Stratified 5-fold Cross-Validation.

Penelitian ini berfokus pada klasifikasi kategori presipitasi pada hari yang sama, bukan prakiraan hujan untuk hari berikutnya.

## 2. Sumber dan Karakteristik Dataset

Dataset diperoleh melalui Open-Meteo Historical Weather API:

https://open-meteo.com/en/docs/historical-weather-api

Model ECMWF IFS dipilih pada bagian Reanalysis Models ketika data diunduh melalui antarmuka Open-Meteo.

Karakteristik dataset yang digunakan:

- Lokasi: titik grid cuaca di sekitar Kota Medan, Sumatera Utara.
- Interval: harian (Daily).
- Tanggal awal analisis: 1 Januari 2023.
- Tanggal akhir analisis: 31 Desember 2025.
- Jumlah observasi: 1.096 hari.
- Format data: CSV.

File sumber disimpan pada:

`data/raw/open_meteo_medan_ecmwf_ifs_daily_2023_2025.csv`

File ekspor Open-Meteo yang digunakan memuat dua baris metadata sebelum header tabel. Oleh karena itu, notebook menggunakan parameter `skiprows=2` ketika membaca CSV.

Dataset merupakan data historis berbasis model/grid untuk titik di sekitar Kota Medan, bukan pengukuran langsung dari stasiun BMKG. Koordinat permintaan dan koordinat grid yang dilaporkan Open-Meteo perlu dicatat pada dokumentasi sumber data.

## 3. Variabel Penelitian

CSV yang digunakan memuat kolom berikut:

| Variabel | Peran dalam eksperimen |
|---|---|
| `date` | Penanda tanggal, tidak digunakan sebagai fitur model |
| `temperature_2m_mean` | Fitur |
| `relative_humidity_2m_mean` | Fitur |
| `cloud_cover_mean` | Fitur |
| `pressure_msl_mean` | Fitur |
| `wind_speed_10m_mean` | Fitur |
| `precipitation_sum` | Pembentuk target |
| `rain_sum` | Variabel tambahan, tidak digunakan sebagai fitur maupun target |

Lima fitur masukan yang digunakan adalah:

1. Suhu rata-rata pada ketinggian 2 meter.
2. Kelembapan relatif rata-rata pada ketinggian 2 meter.
3. Tutupan awan rata-rata.
4. Tekanan permukaan laut rata-rata.
5. Kecepatan angin rata-rata pada ketinggian 10 meter.

Pemilihan fitur merupakan rancangan awal berdasarkan relevansi meteorologis dan ketersediaan variabel pada dataset. Penelitian ini tidak mengklaim bahwa kelima fitur tersebut merupakan kombinasi fitur terbaik untuk seluruh wilayah atau kondisi cuaca.

`precipitation_sum` tidak dimasukkan ke dalam fitur karena variabel tersebut digunakan untuk membentuk label target. `rain_sum` juga dikeluarkan dari fitur model agar kedua variabel presipitasi tidak menjadi masukan yang berkaitan langsung dengan target.

## 4. Pembacaan dan Pembersihan Data

Pengolahan data dilakukan menggunakan Python dan pandas.

Tahapan yang diterapkan dalam notebook meliputi:

1. Membaca CSV dengan melewati dua baris metadata.
2. Merapikan nama kolom dan mengganti nama kolom tanggal menjadi `date`.
3. Mengonversi tanggal menjadi tipe datetime.
4. Mengonversi variabel numerik menjadi tipe numerik.
5. Memastikan kolom yang diperlukan tersedia.
6. Membatasi data pada periode 1 Januari 2023 sampai 31 Desember 2025.
7. Memeriksa tanggal duplikat, nilai kosong, dan tanggal yang hilang.
8. Menghasilkan statistik deskriptif untuk memeriksa karakteristik data.

Hasil pemeriksaan pada dataset yang digunakan:

- Jumlah baris: 1.096.
- Tanggal awal: 1 Januari 2023.
- Tanggal akhir: 31 Desember 2025.
- Tanggal duplikat: 0.
- Tanggal yang hilang: 0.
- Nilai kosong pada seluruh kolom: 0.

Dataset terdiri atas tiga tahun kalender penuh. Tahun 2024 termasuk tahun kabisat, sehingga jumlah observasi yang diharapkan adalah 1.096 hari.

Hasil pemeriksaan ini berlaku untuk CSV yang digunakan dalam eksperimen. Pemeriksaan perlu dijalankan kembali apabila file sumber diganti atau diperbarui.

## 5. Pembentukan Target Klasifikasi

Target dibentuk menggunakan variabel `precipitation_sum` dengan ambang 10 mm.

Aturan klasifikasi:

- Kelas 0: `precipitation_sum < 10 mm`.
- Kelas 1: `precipitation_sum >= 10 mm`.

Ambang 10 mm mengacu pada konsep indeks klimatologi R10mm, yaitu jumlah hari dengan presipitasi harian minimal 10 mm. Dalam proyek ini, ambang tersebut digunakan sebagai dasar pembentukan kelas biner, bukan untuk menghitung indeks R10mm tahunan.

Kelas 0 tidak boleh diartikan sebagai kondisi tanpa hujan. Hari dengan presipitasi di bawah 10 mm, termasuk hari dengan hujan ringan, masuk ke kelas tersebut.

Distribusi target dalam eksperimen:

| Kategori | Jumlah hari | Persentase |
|---|---:|---:|
| Presipitasi < 10 mm | 505 | 46,08% |
| Presipitasi >= 10 mm | 591 | 53,92% |
| Total | 1.096 | 100% |

Kedua kelas memiliki jumlah observasi yang relatif berimbang. Distribusi tersebut kemudian digunakan dalam proses pelatihan dan evaluasi kedua algoritma.

## 6. Penyiapan Fitur dan Target

Fitur input disimpan dalam variabel `X`, sedangkan target disimpan dalam variabel `y`.

Lima fitur yang digunakan:

```python
feature_cols = [
    "temperature_2m_mean",
    "relative_humidity_2m_mean",
    "cloud_cover_mean",
    "pressure_msl_mean",
    "wind_speed_10m_mean",
]
```

Target dibentuk menggunakan:

```python
THRESHOLD_MM = 10.0

df["target"] = (
    df["precipitation_sum"] >= THRESHOLD_MM
).astype("int8")
```

Ukuran data yang digunakan oleh model:

- `X`: 1.096 observasi dan 5 fitur.
- `y`: 1.096 label klasifikasi.

Tanggal dan variabel presipitasi tidak dimasukkan ke dalam `X`.

Dataset hasil penyiapan disimpan pada:

`data/processed/medan_daily_classification_ready.csv`

File ini mempertahankan tanggal, fitur yang digunakan, variabel presipitasi, dan label target untuk membantu pemeriksaan serta reproduksibilitas eksperimen.

## 7. Preprocessing dan Model KNN

Algoritma pertama adalah K-Nearest Neighbors (KNN).

KNN menggunakan jarak antarsampel untuk menentukan kelas. Karena itu, perbedaan skala dan satuan fitur dapat memengaruhi perhitungan jarak. Notebook menerapkan StandardScaler khusus pada pipeline KNN.

Pipeline KNN terdiri atas:

1. `SimpleImputer(strategy="median")`.
2. `StandardScaler()`.
3. `KNeighborsClassifier(n_neighbors=5)`.

Konfigurasi utama KNN:

```python
KNeighborsClassifier(n_neighbors=5)
```

Nilai `n_neighbors=5` dipakai sebagai konfigurasi awal. Penelitian ini tidak melakukan pencarian parameter terbaik atau hyperparameter tuning.

SimpleImputer ditempatkan di dalam pipeline. Meskipun dataset aktual tidak memiliki nilai kosong, langkah ini menjaga alur preprocessing tetap dapat menangani nilai kosong jika ditemukan pada data lain.

## 8. Preprocessing dan Model Decision Tree

Algoritma kedua adalah Decision Tree.

Decision Tree mempelajari aturan pemisahan berdasarkan nilai fitur. StandardScaler tidak diwajibkan untuk algoritma ini karena pembentukan split pohon tidak menggunakan perhitungan jarak seperti KNN.

Pipeline Decision Tree terdiri atas:

1. `SimpleImputer(strategy="median")`.
2. `DecisionTreeClassifier`.

Parameter yang digunakan:

```python
DecisionTreeClassifier(
    max_depth=5,
    min_samples_leaf=5,
    random_state=42
)
```

Kedalaman maksimum lima dan jumlah minimum lima sampel pada setiap daun digunakan sebagai konfigurasi awal untuk membatasi kompleksitas pohon.

Parameter tersebut tidak diklaim sebagai konfigurasi optimal karena eksperimen ini tidak mencakup hyperparameter tuning.

Kedua algoritma menggunakan lima fitur yang sama dan dievaluasi dengan skema validasi yang sama.

## 9. Metode Evaluasi

Evaluasi dilakukan menggunakan Stratified 5-fold Cross-Validation dengan konfigurasi:

```python
StratifiedKFold(
    n_splits=5,
    shuffle=True,
    random_state=42
)
```

Data dibagi menjadi lima fold dengan menjaga proporsi kelas secara relatif seimbang. Pada setiap putaran, satu fold digunakan sebagai data evaluasi dan empat fold lainnya digunakan untuk pelatihan. Prosedur diulangi sampai setiap fold mendapatkan giliran sebagai data evaluasi.

Skema yang sama diterapkan pada KNN dan Decision Tree sehingga hasil kedua model dapat dibandingkan secara konsisten.

Penggunaan `shuffle=True` berarti pembagian fold dilakukan dengan pengacakan yang dapat direproduksi melalui `random_state=42`.

Karena metode ini mengacak pembagian sampel, evaluasi mengukur performa klasifikasi pada fold yang disisihkan, bukan performa prakiraan untuk periode masa depan.

## 10. Metrik Evaluasi

Notebook menggunakan enam metrik evaluasi:

### Accuracy

Mengukur proporsi seluruh prediksi yang benar terhadap seluruh sampel yang dievaluasi.

### Balanced Accuracy

Menghitung rata-rata recall setiap kelas agar evaluasi tidak hanya didominasi oleh kelas yang jumlahnya lebih besar.

### Precision

Precision untuk kelas positif 1 menunjukkan proporsi prediksi kelas presipitasi >=10 mm yang benar.

### Recall

Recall untuk kelas positif 1 menunjukkan proporsi observasi presipitasi >=10 mm yang berhasil dikenali oleh model.

### F1-score

Menggabungkan precision dan recall dalam satu ukuran menggunakan rata-rata harmonik.

### Macro F1

Menghitung F1-score untuk setiap kelas, lalu meratakannya dengan bobot yang sama. Metrik ini membantu membandingkan performa pada kedua kelas.

Notebook menghitung rata-rata dan simpangan baku keenam metrik dari lima fold menggunakan `cross_validate`.

## 11. Confusion Matrix dan Prediksi Out-of-Fold

Selain menghitung skor rata-rata cross-validation, notebook menggunakan `cross_val_predict` untuk menghasilkan prediksi out-of-fold.

Setiap observasi memperoleh prediksi dari model yang tidak menggunakan observasi tersebut sebagai bagian data pelatihan pada fold terkait.

Prediksi tersebut digunakan untuk menghasilkan:

- Confusion matrix KNN.
- Classification report KNN.
- Confusion matrix Decision Tree.
- Classification report Decision Tree.

Confusion matrix digunakan untuk melihat jumlah prediksi benar dan salah pada masing-masing kelas.

Hasil out-of-fold disimpan sebagai visualisasi sehingga perbedaan kesalahan klasifikasi kedua model dapat dibandingkan.

Skor rata-rata lima fold dan skor gabungan prediksi out-of-fold merupakan dua ringkasan yang berbeda. Oleh karena itu, sumber angka yang digunakan pada laporan harus disebutkan dengan jelas.

## 12. Hasil Eksperimen

Rata-rata hasil Stratified 5-fold Cross-Validation pada notebook adalah:

| Metrik | KNN | Decision Tree |
|---|---:|---:|
| Accuracy | 0,724 | 0,728 |
| Balanced accuracy | 0,720 | 0,721 |
| Precision kelas >=10 mm | 0,734 | 0,723 |
| Recall kelas >=10 mm | 0,765 | 0,805 |
| F1-score kelas >=10 mm | 0,749 | 0,761 |
| Macro F1 | 0,721 | 0,722 |

Hasil tersebut merupakan rata-rata dari lima fold. Notebook juga menyimpan simpangan baku masing-masing metrik.

Secara deskriptif, Decision Tree memperoleh accuracy dan Macro F1 rata-rata yang sedikit lebih tinggi daripada KNN. KNN memperoleh precision yang lebih tinggi untuk kelas presipitasi >=10 mm, sedangkan Decision Tree memperoleh recall yang lebih tinggi pada kelas tersebut.

Perbedaan performa kedua model relatif kecil. Hasil ini tidak membuktikan bahwa salah satu algoritma secara mutlak atau signifikan lebih unggul daripada algoritma lainnya.

## 13. File Hasil yang Dihasilkan

Notebook menghasilkan file berikut.

### Dataset hasil penyiapan

`data/processed/medan_daily_classification_ready.csv`

### Distribusi target

`reports/figures/distribusi_target.png`

### Confusion matrix KNN

`reports/figures/confusion_matrix_knn.png`

### Confusion matrix Decision Tree

`reports/figures/confusion_matrix_decision_tree.png`

### Perbandingan Macro F1

`reports/figures/perbandingan_macro_f1.png`

### Ringkasan cross-validation

`reports/tables/knn_vs_decision_tree_cv_summary.csv`

### Hasil setiap fold

`reports/tables/cv_scores_per_fold.csv`

Seluruh file tersebut dihasilkan dari kode notebook dan digunakan sebagai bahan pemeriksaan hasil serta penyusunan laporan penelitian.

## 14. Keterbatasan Penelitian

Penelitian ini memiliki beberapa keterbatasan.

1. Dataset berasal dari data historis berbasis model/grid pada satu titik di sekitar Kota Medan, bukan pengukuran langsung dari stasiun BMKG.
2. Lima fitur meteorologis yang digunakan merupakan kandidat awal dan belum melalui eksperimen seleksi fitur.
3. Target berupa kategori presipitasi pada hari yang sama dengan fitur, sehingga penelitian tidak mengukur kemampuan memprediksi hujan pada hari berikutnya.
4. Stratified 5-fold Cross-Validation menggunakan pengacakan. Hasilnya tidak boleh diinterpretasikan sebagai evaluasi model pada periode waktu yang lebih baru.
5. Parameter KNN dan Decision Tree merupakan konfigurasi awal yang belum melalui hyperparameter tuning.
6. Penelitian hanya membandingkan dua algoritma dasar sesuai tujuan tugas dan bukan membangun sistem prakiraan cuaca operasional.

## 15. Reproduksibilitas

Untuk menjalankan eksperimen kembali:

1. Pastikan CSV sumber berada pada:

   `data/raw/open_meteo_medan_ecmwf_ifs_daily_2023_2025.csv`

2. Instal dependensi yang tercantum dalam `requirements.txt`.

3. Buka notebook:

   `notebooks/01_klasifikasi_knn_vs_decision_tree.ipynb`

4. Jalankan sel notebook secara berurutan dari awal hingga akhir.

Notebook akan membaca CSV, memeriksa data, membentuk target, menyiapkan fitur, melakukan cross-validation, menghasilkan metrik, dan menyimpan artefak ke folder `data/processed/` serta `reports/`.

Hasil yang dilaporkan harus berasal dari eksekusi notebook dengan CSV yang sama. Jika data, parameter, atau konfigurasi validasi berubah, notebook perlu dijalankan kembali dan tabel hasil diperbarui.

## 16. Referensi

1. Oliveira et al. (2023). "Precipitation forecasting: from geophysical aspects to machine learning applications." *Frontiers in Climate*, 5, 1250201. https://doi.org/10.3389/fclim.2023.1250201

2. Wani et al. (2024). "Predicting rainfall using machine learning, deep learning, and time series models across an altitudinal gradient in the North-Western Himalayas." *Scientific Reports*, 14, 27876. https://doi.org/10.1038/s41598-024-77687-x

3. Birant et al. (2025). "A new machine learning method for rainfall classification: temporal random tree." *PeerJ Computer Science*, 11, e3022. https://doi.org/10.7717/peerj-cs.3022

4. Climdex. "Climate indices and definitions." https://www.climdex.org/learn/indices/

5. Open-Meteo. "Historical Weather API Documentation." https://open-meteo.com/en/docs/historical-weather-api

6. scikit-learn. "Cross-validation: evaluating estimator performance." https://scikit-learn.org/stable/modules/cross_validation.html

7. scikit-learn. "StandardScaler." https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html
