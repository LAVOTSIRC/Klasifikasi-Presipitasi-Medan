# Sumber Dataset

## Penyedia

Open-Meteo Historical Weather API
https://open-meteo.com/en/docs/historical-weather-api

## Pengaturan Data

- Model: ECMWF IFS (dipilih pada antarmuka saat pengambilan)
- Interval: Daily
- Koordinat permintaan: 3.55, 98.68
- Koordinat grid pada metadata CSV: catat sesuai file aktual
- Zona waktu: Asia/Jakarta
- Tanggal awal: 2023-01-01
- Tanggal akhir: 2026-01-01
- Format: CSV

Catatan: pilihan model dicatat dari pengaturan saat unduh.
Jangan menganggap metadata CSV sendiri membuktikan model
yang dipilih apabila nama model tidak tercantum di dalamnya.
Simpan URL permintaan atau screenshot pengaturan sebagai
bukti provenance jika tersedia.

## Variabel

- temperature_2m_mean
- relative_humidity_2m_mean
- cloud_cover_mean
- pressure_msl_mean
- wind_speed_10m_mean
- precipitation_sum
- rain_sum

## Penggunaan

Lima fitur meteorologis digunakan sebagai input model.
Target dibentuk dari precipitation_sum dengan ambang 10 mm.
precipitation_sum dan rain_sum tidak dimasukkan sebagai
fitur input dalam eksperimen utama.

CSV mentah dipertahankan tanpa perubahan. Pembatasan periode,
pembentukan target, dan preprocessing dilakukan di notebook.

## Keterbatasan

Data merupakan data historis berbasis model/grid dan tidak
boleh diklaim sebagai pengukuran langsung stasiun BMKG.
