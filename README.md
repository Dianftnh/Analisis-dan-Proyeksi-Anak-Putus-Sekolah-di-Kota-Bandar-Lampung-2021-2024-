# 📊 Analisis dan Proyeksi Anak Putus Sekolah di Kota Bandar Lampung (2021–2024)

Proyek analisis data end-to-end tentang anak putus sekolah di Kota Bandar Lampung: mulai dari penilaian kualitas data, pembersihan, analisis eksplorasi mendalam, hingga proyeksi 2025 menggunakan regresi linear.

[Peta panas kasus putus sekolah per kecamatan dan tahun](assets/05_peta_panas_kecamatan_tahun.png)

---

## 🔑 Temuan Utama

- **Kasus turun 47% dari puncak 2022** (159 → 85 anak). Dibanding 2023, turun 6,6%. Total **437 kasus** selama 2021–2024.
- **13 dari 20 kecamatan membaik** pada 2024; hanya 3 yang naik.
- **Hotspot kumulatif menyesatkan.** Tanjung Senang menempati peringkat 1 (50 kasus), tetapi 31 kasusnya terjadi di satu tahun (2022) dan sudah turun menjadi 6 pada 2024. **Hotspot terbaru adalah Teluk Betung Timur: 23 kasus pada 2024 (27% dari seluruh kasus tahun itu).**
- **Kenaikan SD pada 2024 bersifat lokal.** SD naik +19, sama persis dengan kenaikan di Teluk Betung Timur (+19). Tanpa kecamatan itu, kasus SD tidak berubah (52 → 52).
- **Titik rawan ada pada peralihan jenjang:** kelas 7 menyumbang 62% kasus SMP dan kelas 10 menyumbang 57% kasus SMA.
- **Data mentah menghitung kasus dua kali.** Penjumlahan langsung menghasilkan 922, sedangkan angka yang benar adalah 437 (lihat [Catatan Kualitas Data](#️-catatan-kualitas-data-penting)).

---

## 🎯 Tujuan dan Pertanyaan Bisnis

Menganalisis angka putus sekolah di Bandar Lampung berdasarkan jenjang pendidikan, tipe sekolah, dan wilayah (kecamatan), serta membangun model regresi sederhana untuk memproyeksikan tren ke depan.

1. Bagaimana tren jumlah anak putus sekolah dari tahun ke tahun?
2. Jenjang pendidikan apa yang menyumbang angka putus sekolah tertinggi?
3. Kecamatan mana saja yang menjadi *hotspot* (Top 5 wilayah dengan kasus terbanyak)?
4. Bagaimana perbandingan angka putus sekolah antara sekolah Negeri vs Swasta?
5. Bagaimana proyeksi total anak putus sekolah pada tahun 2025 berdasarkan data historis?

**Eksplorasi lanjutan (pertanyaan tambahan):**

- E1. Apakah hotspot kumulatif sama dengan hotspot terbaru?
- E2. Apa yang menyebabkan kenaikan kasus SD pada 2024?
- E3. Bagaimana pola jenis kelamin per jenjang?
- E4. Pada kelas berapa anak paling rawan putus sekolah?
- E5. Seberapa terkonsentrasi kasusnya, dan kecamatan mana yang perlu diprioritaskan?

---

## 📂 Struktur Repositori

```
├── dataset_all.csv                      # Data mentah gabungan (SD, SMP, SMA, SMK)
├── dataset_all_clean.csv                # Data bersih, siap analisis
├── Analisis_Putus_Sekolah_v3.ipynb      # Notebook utama (gathering, assessing, cleaning, EDA, modeling)
├── output_looker/                       # File ringkasan siap pakai untuk dashboard (pengembangan lanjutan)
│   ├── dashboard_kpi.csv
│   ├── kecamatan_status.csv
│   └── tren_dan_proyeksi.csv
├── assets/                              # Visualisasi hasil notebook
└── README.md
```

---

## 🗂️ Dataset

**Sumber:** [opendata.lampungprov.go.id](https://opendata.lampungprov.go.id)

| Kolom | Keterangan |
|---|---|
| `Kecamatan` | Kecamatan di Kota Bandar Lampung (20 kecamatan) |
| `Jenjang` | SD, SMP, SMA, SMK |
| `Tahun` | 2021–2024 |
| `Jumlah` | Jumlah anak putus sekolah |
| `Jenis Kelamin` | Laki-laki, Perempuan |
| `Tipe Sekolah` | Negeri / Swasta. **Hanya tersedia untuk SD**, selain itu `Unknown` |
| `Tingkat` | Kelas (VII–IX untuk SMP, X–XII untuk SMA/SMK). **Tidak tersedia untuk SD**, diisi `Unknown` |
| `Kelas` | Kolom bantu hasil pembersihan: Kelas 07 … Kelas 12 (berurutan agar urutan di grafik benar), `Unknown` untuk SD |

---

## 🛠️ Alur Kerja (Pipeline)

1. **Data Gathering and Understanding:** memuat data mentah, memeriksa tipe data dan *missing values*.
2. **Data Assessing:** memeriksa duplikat dan struktur label tiap jenjang. Tahap ini menemukan masalah perhitungan ganda.
3. **Data Cleaning and Wrangling:**
   - Mengimputasi nilai kosong `Tipe Sekolah` dan `Tingkat` dengan `Unknown` (kosong bersifat struktural, bukan data hilang).
   - Merapikan teks dan menyeragamkan nama kecamatan (menghapus awalan "Kec.").
   - Menghapus baris `Total` dan duplikatnya, hanya menyimpan baris rincian.
   - Memvalidasi hasil terhadap total asli yang ada di data.
   - Menambahkan kolom bantu `Kelas`.
4. **Exploratory Data Analysis (EDA):** tren tahunan, jenjang, *hotspot* kecamatan, negeri vs swasta.
5. **Eksplorasi Lanjutan (Deep Dive):** peta panas kecamatan × tahun, sumber kenaikan SD, pola gender, kelas rawan, konsentrasi kasus (Pareto), dan status prioritas kecamatan.
6. **Data Modeling:** Linear Regression untuk proyeksi 2025, dievaluasi dengan MAE, RMSE, R², *Leave-One-Out*, dan interval prediksi 95%.
7. **Ekspor:** menghasilkan file ringkasan (KPI, status kecamatan, tren + proyeksi) yang siap dipakai untuk dashboard.

---

## ⚠️ Catatan Kualitas Data (Penting)

Dataset mentah **tidak boleh dijumlahkan langsung**, karena `Jumlah` akan terhitung ganda:

- Setiap jenjang memuat **baris rincian dan baris `Total` sekaligus** (SD: Negeri + Swasta + Total; SMP/SMA/SMK: per kelas + Total).
- Seluruh **500 baris duplikat** berasal dari jenjang **SMK**. Baris `Total` SMK adalah salinan berulang (rata-rata 5×) dari satu nilai yang sama.
- Untuk SMK, nilai `Total` yang sebenarnya cocok **100%** dengan penjumlahan kelas X + XI + XII pada seluruh 130 kombinasi (kecamatan, tahun, gender), sehingga rincian per kelas dipakai.

| | Penjumlahan langsung (salah) | Setelah dibersihkan |
|---|---|---|
| Jumlah baris | 2.744 | 1.628 |
| Total kasus | 922 | **437** |

Validasi: total SD (273), SMP (61), dan SMA (79) hasil bersih sama persis dengan baris `Total` asli di data.

> SMA tercatat di 18–19 kecamatan dan SMK di 16–17 kecamatan. Kecamatan yang tidak muncul diasumsikan tidak memiliki sekolah pada jenjang tersebut.

---

## 📈 Hasil Analisis

### 1. Tren tahunan

![Tren tahunan](assets/01_tren_tahunan.png)

| Tahun | 2021 | 2022 | 2023 | 2024 |
|---|---|---|---|---|
| Jumlah | 102 | **159** | 91 | 85 |

### 2. Per jenjang

![Kasus per jenjang](assets/02_kasus_per_jenjang.png)

| Jenjang | Total | Persentase | 2024 |
|---|---|---|---|
| SD | **273** | 62,5% | 75 (88% dari kasus 2024) |
| SMA | 79 | 18,1% | 4 |
| SMP | 61 | 14,0% | 3 |
| SMK | 24 | 5,5% | 3 |

### 3. Hotspot: kumulatif vs terbaru

![Top 5 kecamatan](assets/03_top5_kecamatan.png)

Peringkat kumulatif (di atas) menggabungkan semua tahun. Peta panas kecamatan × tahun (di bagian atas halaman ini) menunjukkan gambaran yang berbeda:

| Kecamatan | 2021 | 2022 | 2023 | 2024 | Total |
|---|---|---|---|---|---|
| Tanjung Senang | 5 | **31** | 8 | 6 | 50 |
| Teluk Betung Timur | 5 | 3 | 4 | **23** | 35 |
| Rajabasa | 5 | 16 | 4 | 4 | 29 |
| Tanjung Karang Timur | 12 | 8 | 4 | 3 | 27 |
| Tanjung Karang Pusat | 5 | 10 | 8 | 3 | 26 |
| Teluk Betung Utara | 7 | 8 | 7 | 4 | 26 |

Tanjung Karang Pusat dan Teluk Betung Utara sama-sama berada di peringkat 5 (imbang).

### 4. Sumber kenaikan SD pada 2024

![Kontribusi kenaikan SD](assets/06_kontribusi_kenaikan_sd.png)

Kenaikan bersih SD (+19) sama persis dengan kenaikan di Teluk Betung Timur (+19). Tanpa kecamatan ini, kasus SD tidak berubah (52 → 52).

### 5. Profil anak

![Gender per jenjang](assets/07_gender_per_jenjang.png)

- **Jenis kelamin:** SD 59% laki-laki; SMP 64% perempuan. Pada 2024, anak laki-laki mencapai 65% dari seluruh kasus (55 dari 85).

![Kelas rawan](assets/08_kelas_rawan.png)

- **Kelas rawan:** kelas 7 (38 dari 61 kasus SMP) dan kelas 10 (45 dari 79 kasus SMA), yaitu saat peralihan jenjang.

![Negeri vs swasta (SD)](assets/04_negeri_vs_swasta_sd.png)

- **Negeri vs swasta (khusus SD):** Negeri 243, Swasta 30. Angka ini belum dibagi jumlah siswa, sehingga belum menunjukkan risiko secara proporsional.

### 6. Konsentrasi dan status prioritas kecamatan

![Pareto kecamatan](assets/09_pareto_kecamatan.png)

Delapan dari 20 kecamatan menyumbang 50% kasus, dan 14 kecamatan menyumbang 80%.

| Status | Aturan | Kecamatan |
|---|---|---|
| Waspada | Kasus 2024 naik dibanding 2023 dan ≥ 6 | Teluk Betung Timur, Sukabumi, Way Halim |
| Pantau | Kasus 2024 ≥ 5, tidak naik | Tanjung Senang, Panjang |
| Terkendali | Selain di atas | 15 kecamatan lainnya |

Ambang (≥ 6 dan ≥ 5) adalah aturan sederhana yang dapat disesuaikan.

---

## 🔮 Proyeksi 2025

![Proyeksi 2025](assets/10_proyeksi_2025.png)

| Metrik | Nilai |
|---|---|
| Proyeksi 2025 | **~80 anak** |
| Rentang prediksi 95% | 0 – 331 |
| Slope | −11,90 anak/tahun |
| MAE / RMSE (in-sample) | 21,90 / 26,18 |
| R² | 0,21 |
| MAE *Leave-One-Out* (regresi vs tebakan rata-rata) | 46,29 vs 33,17 |

**Cara membaca:** model hanya memakai 4 titik data tahunan. Pada uji *Leave-One-Out*, regresi tidak lebih akurat daripada tebakan rata-rata, dan rentang prediksinya sangat lebar. Proyeksi ini sebaiknya dipahami sebagai **perkiraan kasar arah tren (cenderung menurun)**, bukan target pasti.

---

## 💻 Cara Menjalankan

### 1. Prasyarat

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn
```

### 2. Menjalankan notebook

**Google Colab:** unggah `Analisis_Putus_Sekolah_v3.ipynb`, jalankan sel dari atas, lalu unggah `dataset_all.csv` saat diminta. File hasil akan terunduh otomatis di akhir.

**Lokal:** letakkan `dataset_all.csv` di folder yang sama dengan notebook, lalu jalankan semua sel.

Notebook menghasilkan:
- `dataset_all_clean.csv`
- `output_looker/dashboard_kpi.csv`
- `output_looker/kecamatan_status.csv`
- `output_looker/tren_dan_proyeksi.csv`

### 3. Cuplikan inti proyeksi

```python
import pandas as pd
from sklearn.linear_model import LinearRegression

df = pd.read_csv('dataset_all_clean.csv')

# Data sudah bebas baris 'Total', sehingga aman dijumlahkan langsung
df_regresi = df.groupby('Tahun')['Jumlah'].sum().reset_index()

model = LinearRegression().fit(df_regresi[['Tahun']], df_regresi['Jumlah'])
hasil_2025 = model.predict(pd.DataFrame({'Tahun': [2025]}))[0]
print(f"Proyeksi 2025: {round(hasil_2025)} anak")
```

---

## ✅ Rekomendasi

- **Sistem peringatan dini per kecamatan:** total yang turun dapat menyembunyikan lonjakan lokal (Tanjung Senang 2022, Teluk Betung Timur 2024). Pantau kasus per kecamatan secara berkala dan tandai kecamatan berstatus **Waspada**: Teluk Betung Timur, Sukabumi, dan Way Halim.
- **Telusuri penyebab di Teluk Betung Timur:** verifikasi lapangan atas 23 kasus SD pada 2024 (lonjakan nyata, perubahan pencatatan, atau faktor lokal) sebelum menentukan bentuk intervensi.
- **Pendampingan masa transisi:** fokuskan dukungan (bantuan biaya, pendampingan, pemantauan kehadiran) pada anak yang baru masuk kelas 7 dan kelas 10.
- **Pendekatan berbeda menurut jenjang dan gender:** kaji penyebab pada anak laki-laki di SD dan anak perempuan di SMP, karena polanya berlawanan.
- **Pelajari kecamatan yang membaik:** misalnya Kedamaian (16 kasus pada 2021 menjadi 2 pada 2024) dan Labuhan Ratu (11 menjadi 1), untuk direplikasi ke wilayah lain.
- **Perbaikan data:** tambahkan data jumlah siswa agar risiko dapat dihitung sebagai rasio, hindari baris `Total` yang bercampur dengan rincian dan duplikasi data SMK, serta gunakan data semesteran agar proyeksi lebih andal.

---

## 🚧 Keterbatasan

- Hanya 4 tahun data, sehingga model proyeksi tidak stabil.
- Jumlah kasus belum dinormalisasi dengan jumlah siswa, jadi belum bisa dibandingkan secara proporsional antar kecamatan, jenjang, atau tipe sekolah.
- Perbandingan negeri vs swasta hanya tersedia untuk SD.
- Kelengkapan data SMA dan SMK tidak merata di semua kecamatan.
- Angka per kecamatan kecil, sehingga satu kejadian dapat mengubah peringkat secara drastis. Interpretasi per kecamatan perlu kehati-hatian.

---

## 🗺️ Rencana Pengembangan

- Membuat dashboard interaktif (Looker Studio) dari file di folder `output_looker/`.
- Menambahkan data jumlah siswa untuk menghitung angka putus sekolah sebagai rasio.
- Menambahkan data periode yang lebih panjang atau semesteran untuk memperkuat model proyeksi.

---

## 🧰 Teknologi

Python · pandas · NumPy · Matplotlib · Seaborn · SciPy · scikit-learn · Jupyter / Google Colab

---

## 👥 Kontribusi dan Lisensi

Kontribusi, saran, maupun kritik yang membangun sangat dipersilakan. Silakan buka *Issues* atau ajukan *Pull Request*.

Data bersumber dari [opendata.lampungprov.go.id](https://opendata.lampungprov.go.id); mohon merujuk ke ketentuan penggunaan data pada situs tersebut.
