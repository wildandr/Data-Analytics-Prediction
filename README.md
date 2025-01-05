# Laporan Analisis Pembatalan Reservasi Hotel

## 1. Pendahuluan

### 1.1 Pernyataan Masalah
Dalam beberapa tahun terakhir, **City Hotel** dan **Resort Hotel** mengalami peningkatan signifikan dalam tingkat pembatalan reservasi. Masalah ini menyebabkan beragam tantangan, termasuk penurunan pendapatan dan rendahnya tingkat okupansi kamar hotel. Oleh karena itu, prioritas utama bagi kedua hotel adalah mengurangi tingkat pembatalan reservasi untuk meningkatkan efisiensi dalam menghasilkan pendapatan. Analisis ini bertujuan untuk memahami faktor-faktor yang memengaruhi pembatalan reservasi dan memberikan rekomendasi yang relevan untuk meminimalkan dampaknya.

### 1.2 Rencana Analisis
Pendekatan analisis akan dilakukan dengan menggunakan dataset yang mencakup **119.390 entri** reservasi hotel antara 1 Juli 2015 hingga 31 Agustus 2017. Data ini mencakup berbagai informasi, seperti:
- Jenis hotel: **City Hotel** atau **Resort Hotel**
- Informasi reservasi: waktu kedatangan, lama menginap, status pembatalan, dan lainnya.
- Karakteristik pelanggan: jumlah tamu dewasa, anak-anak, permintaan khusus, dan sebagainya.

Metodologi yang digunakan meliputi eksplorasi data, analisis statistik, dan visualisasi untuk mengidentifikasi pola, tren, serta faktor utama yang memengaruhi pembatalan reservasi.

### 1.3 Pendekatan dan Teknik Analisis
Analisis ini akan mencakup:
1. **Eksplorasi Data (Exploratory Data Analysis)**: Mengidentifikasi distribusi data, hubungan antar variabel, dan pola pembatalan reservasi.
2. **Visualisasi Data**: Menyajikan tren utama melalui grafik, seperti tingkat pembatalan berdasarkan jenis hotel, musim, dan harga rata-rata harian (Average Daily Rate/ADR).
3. **Analisis Statistik**: Menghitung persentase pembatalan, menganalisis hubungan harga dengan pembatalan, serta mengidentifikasi segmen pasar yang paling rentan terhadap pembatalan.

### 1.4 Manfaat Analisis
Hasil analisis ini akan memberikan manfaat berikut:
- **Strategi Penetapan Harga**: Memberikan rekomendasi berdasarkan pola pembatalan untuk meningkatkan pendapatan dan okupansi hotel.
- **Keputusan Promosi**: Mengidentifikasi waktu dan tempat yang membutuhkan promosi lebih intensif.
- **Peningkatan Pelayanan**: Memberikan wawasan tentang preferensi pelanggan untuk meningkatkan pengalaman mereka selama menginap.
- **Efisiensi Operasional**: Mengurangi pemborosan sumber daya akibat pembatalan mendadak.

## 2. Package yang Diperlukan

### 2.1 Daftar Package yang Digunakan
Analisis ini dilakukan menggunakan beberapa package Python. Untuk memastikan pembaca dapat mereplikasi analisis ini, berikut adalah daftar package yang digunakan dan fungsinya:

```python
# Import semua package yang diperlukan
import pandas as pd                # Untuk manipulasi data dan analisis
import matplotlib.pyplot as plt    # Untuk membuat visualisasi data
import seaborn as sns              # Untuk visualisasi data yang lebih interaktif
import warnings                    # Untuk menghilangkan pesan peringatan
```

### 2.2 Menghilangkan Pesan dan Peringatan
Untuk menjaga agar output tetap bersih dan bebas dari pesan peringatan selama analisis, perintah berikut digunakan:

```python
# Menghilangkan peringatan
warnings.filterwarnings('ignore')
```

### Cara Instalasi Package
Jika package di atas belum terinstal di sistem Anda, gunakan perintah berikut untuk menginstalnya:

```bash
pip install pandas matplotlib seaborn
```

### Penjelasan Fungsi Package
- **`pandas`**: Memanipulasi dataset, seperti memuat data, membersihkan data, dan analisis berbasis tabel.
- **`matplotlib`**: Membuat grafik sederhana hingga kompleks untuk menganalisis data.
- **`seaborn`**: Visualisasi tingkat lanjut, seperti heatmap, countplot, dan distribusi data.
- **`warnings`**: Digunakan untuk mengelola atau menyembunyikan pesan peringatan yang tidak relevan selama proses analisis.

## 3. Data Preparation

### 3.1 Sumber Data
Data yang digunakan dalam analisis ini berasal dari [TidyTuesday](https://raw.githubusercontent.com/rfordatascience/tidytuesday/main/data/2020/2020-02-11/hotels.csv). Dataset ini berisi informasi tentang reservasi hotel di dua jenis hotel berbeda, yaitu **City Hotel** dan **Resort Hotel**, antara **1 Juli 2015 hingga 31 Agustus 2017**.

### 3.2 Deskripsi Data
Dataset ini awalnya dikumpulkan untuk menganalisis pola reservasi hotel, termasuk pembatalan, durasi menginap, dan preferensi pelanggan. Dataset mencakup **119.390 observasi** dan **32 variabel**, dengan rincian sebagai berikut:
- **Tujuan awal data**: Untuk memahami pola reservasi, tingkat pembatalan, dan faktor yang memengaruhi efisiensi hotel.
- **Jumlah variabel**: Dataset mencakup variabel seperti `hotel`, `is_canceled`, `lead_time`, `arrival_date`, `adr` (Average Daily Rate), dan lainnya.
- **Kekhasan data**:
  - Nilai hilang: Dicatat sebagai `NaN`, ditemukan pada variabel seperti `agent`, `company`, dan `country`.
  - Skala data: Beberapa variabel seperti `adr` memiliki skala numerik, sementara `hotel` dan `reservation_status` adalah kategorikal.
  
Dataset ini mencerminkan pola reservasi nyata dengan beberapa aspek anonim untuk menjaga kerahasiaan.

### 3.3 Langkah-Langkah Pembersihan Data
Berikut adalah langkah-langkah pembersihan data yang dilakukan untuk memastikan analisis dapat dilakukan dengan optimal:
1. **Mengimpor data**:
   Data diimpor menggunakan `pandas`:
   ```python
   import pandas as pd
   url = "https://raw.githubusercontent.com/rfordatascience/tidytuesday/main/data/2020/2020-02-11/hotels.csv"
   df = pd.read_csv(url)
   ```

2. **Menghapus variabel yang tidak relevan**:
   Variabel `agent` dan `company` dihapus karena sebagian besar nilai adalah `NaN` (data tidak lengkap):
   ```python
   df.drop(['agent', 'company'], axis=1, inplace=True)
   ```

3. **Menangani nilai hilang**:
   Baris dengan nilai hilang dihapus untuk menjaga integritas data:
   ```python
   df.dropna(inplace=True)
   ```

4. **Membatasi nilai ekstrim**:
   Beberapa entri pada variabel `adr` (Average Daily Rate) memiliki nilai yang terlalu tinggi (>5000) yang dapat mengganggu analisis. Baris tersebut dihapus:
   ```python
   df = df[df['adr'] < 5000]
   ```

### 3.4 Dataset Setelah Pembersihan
Setelah langkah-langkah pembersihan, dataset terdiri dari **118.897 observasi** dan **30 variabel**. Berikut adalah pratinjau dari dataset yang telah dibersihkan:

| hotel         | is_canceled | lead_time | arrival_date_year | adr   | adults | children | total_of_special_requests |
|---------------|-------------|-----------|--------------------|-------|--------|----------|---------------------------|
| Resort Hotel  | 0           | 342       | 2015               | 0.00  | 2      | 0.0      | 0                         |
| City Hotel    | 1           | 102       | 2017               | 225.43| 3      | 0.0      | 2                         |
| Resort Hotel  | 0           | 7         | 2015               | 75.00 | 1      | 0.0      | 0                         |
| City Hotel    | 1           | 205       | 2017               | 151.20| 2      | 0.0      | 2                         |
| Resort Hotel  | 0           | 14        | 2015               | 98.00 | 2      | 0.0      | 1                         |

### 3.5 Ringkasan Variabel Penting
Berikut adalah variabel utama yang dianalisis:
- **hotel**: Jenis hotel (`City Hotel` atau `Resort Hotel`).
- **is_canceled**: Status pembatalan (0 = Tidak dibatalkan, 1 = Dibatalkan).
- **lead_time**: Waktu antara pemesanan dan tanggal kedatangan.
- **adr**: Tarif rata-rata harian (dalam Euro).
- **adults** dan **children**: Jumlah tamu dewasa dan anak-anak dalam setiap reservasi.
- **total_of_special_requests**: Jumlah permintaan khusus dari pelanggan.

### Statistik Ringkasan
Berikut adalah statistik ringkasan untuk beberapa variabel numerik:

| Variabel                   | Rata-rata | Min   | Maks  | Standar Deviasi |
|----------------------------|-----------|-------|-------|------------------|
| is_canceled                | 0.37      | 0     | 1     | 0.48             |
| lead_time                  | 104.31    | 0     | 737   | 106.90           |
| adr                        | 101.96    | -6.38 | 510.0 | 48.09            |
| total_of_special_requests  | 0.57      | 0     | 5     | 0.79             |

Dataset bersih ini siap digunakan untuk analisis eksplorasi ataupun pengembangan model.

## 4. Eksplorasi dan Analisis Data

### 4.1 Pendahuluan
Bagian ini berfokus pada eksplorasi dataset dan menganalisis pola serta tren yang relevan untuk memahami faktor yang memengaruhi pembatalan reservasi hotel. Kami tidak hanya memvisualisasikan data sebagaimana adanya, tetapi juga melakukan transformasi, agregasi, dan pembuatan variabel baru untuk menemukan wawasan yang lebih mendalam.

Beberapa poin utama eksplorasi ini mencakup:
- Menganalisis tingkat pembatalan berdasarkan jenis hotel, musim, dan lama waktu reservasi.
- Membuat variabel baru seperti **Total Guests** dan **Cancellation Rate** untuk menyederhanakan analisis.
- Menampilkan tren harga rata-rata harian (Average Daily Rate/ADR) dan hubungannya dengan tingkat pembatalan.

### 4.2 Metodologi Visualisasi
Visualisasi data dirancang untuk menyampaikan temuan dengan cara yang mudah dipahami, mencakup:
1. **Grafik Bar dan Pie**: Digunakan untuk menunjukkan distribusi kategori seperti status pembatalan, asal pelanggan, dan metode pemesanan.
2. **Grafik Garis**: Untuk mengamati tren waktu, seperti perubahan harga rata-rata harian (ADR) dan tingkat pembatalan selama periode tertentu.
3. **Tabel Ringkasan**: Digunakan untuk perbandingan antar kategori, seperti tingkat pembatalan antara **City Hotel** dan **Resort Hotel**.

### 4.3 Pengantar Visualisasi
Setiap visualisasi dirancang dengan fokus pada poin-poin utama berikut:
- **Grafik Status Pembatalan**: Memberikan gambaran umum tentang proporsi pembatalan dan non-pembatalan pada kedua jenis hotel.
- **Tingkat Pembatalan Berdasarkan Jenis Hotel**: Menyoroti bahwa **Resort Hotel** memiliki tingkat pembatalan yang lebih rendah dibandingkan **City Hotel**.
- **Tren Harga Harian (ADR)**: Menunjukkan bagaimana perubahan harga memengaruhi keputusan pembatalan pelanggan.
- **Distribusi Pembatalan per Bulan**: Mengidentifikasi bulan dengan tingkat pembatalan tertinggi dan terendah.
- **Sumber Reservasi**: Menganalisis peran agen perjalanan online (OTA) dan grup dalam pembatalan.

### 4.4 Contoh Penggunaan Visualisasi
- **Grafik Bar**:
  - Menggambarkan distribusi status pembatalan (`Cancelled` vs. `Not Cancelled`).
  - Mengidentifikasi bulan dengan tingkat reservasi tertinggi dan terendah.
- **Grafik Pie**:
  - Menunjukkan proporsi pembatalan berdasarkan negara asal pelanggan.
- **Grafik Garis**:
  - Menyoroti perubahan harga rata-rata harian (ADR) selama waktu tertentu, baik untuk pembatalan maupun non-pembatalan.

### 4.5 Wawasan dan Interpretasi
Setelah eksplorasi data, beberapa wawasan utama yang ditemukan meliputi:
1. **Tingkat Pembatalan**:
   - **City Hotel** memiliki tingkat pembatalan lebih tinggi dibandingkan **Resort Hotel**.
   - **Bulan Januari** memiliki pembatalan tertinggi, sementara **Bulan Agustus** menunjukkan reservasi terbanyak.
   
2. **Pengaruh Harga (ADR)**:
   - Pembatalan lebih mungkin terjadi ketika harga rata-rata harian (ADR) tinggi.
   - Diskon dan promosi pada waktu tertentu dapat membantu mengurangi tingkat pembatalan.

3. **Sumber Reservasi**:
   - Mayoritas reservasi berasal dari agen perjalanan online (Online Travel Agencies/OTA), yang juga memiliki proporsi pembatalan tertinggi.

4. **Segmentasi Pelanggan**:
   - Kelompok pelanggan tertentu, seperti grup, lebih rentan terhadap pembatalan dibandingkan pelanggan individu.

## 5. Rangkuman

### 5.1 Pernyataan Masalah
Masalah utama yang dianalisis dalam proyek ini adalah **tingginya tingkat pembatalan reservasi pada City Hotel dan Resort Hotel** dalam beberapa tahun terakhir. Tingkat pembatalan yang tinggi menyebabkan penurunan pendapatan dan rendahnya tingkat okupansi kamar, sehingga menjadi prioritas utama untuk diatasi.

### 5.2 Pendekatan yang Digunakan
Kami membahas pernyataan masalah ini dengan langkah-langkah berikut:
- **Data yang Digunakan**: Dataset berisi 119.390 entri reservasi hotel antara Juli 2015 hingga Agustus 2017, mencakup informasi seperti jenis hotel, status pembatalan, harga rata-rata harian (ADR), dan metode pemesanan.
- **Metodologi**:
  - **Pembersihan Data**: Menghapus nilai hilang, variabel yang tidak relevan, dan nilai ekstrim untuk meningkatkan kualitas analisis.
  - **Eksplorasi Data**: Menggunakan visualisasi seperti grafik batang, garis, dan pie untuk mengidentifikasi pola dan tren.
  - **Analisis Statistik**: Menghitung tingkat pembatalan dan hubungan variabel utama seperti harga dan waktu reservasi dengan tingkat pembatalan.

### 5.3 Wawasan Menarik dari Analisis
Analisis menghasilkan beberapa wawasan menarik:
1. **Tingkat Pembatalan**:
   - **City Hotel** memiliki tingkat pembatalan lebih tinggi (41,7%) dibandingkan **Resort Hotel** (27,9%).
   - Bulan dengan tingkat pembatalan tertinggi adalah **Januari**, sedangkan **Agustus** menunjukkan reservasi terbanyak.
   
2. **Pengaruh Harga (ADR)**:
   - Pembatalan lebih sering terjadi ketika harga rata-rata harian (ADR) tinggi, mengindikasikan bahwa strategi harga memainkan peran penting dalam pembatalan.

3. **Sumber Reservasi**:
   - Mayoritas reservasi berasal dari **Online Travel Agencies (OTA)**, yang juga memiliki proporsi pembatalan tertinggi.
   - Reservasi dari grup menunjukkan tingkat pembatalan yang lebih tinggi dibandingkan pelanggan individu.

4. **Segmentasi Pelanggan**:
   - Pelanggan dengan lebih banyak permintaan khusus cenderung memiliki tingkat pembatalan lebih rendah.

### 5.4 Implikasi Analisis terhadap Konsumen
Hasil analisis memberikan panduan strategis berikut bagi manajemen hotel:
- **Strategi Harga**:
  - Menurunkan harga pada waktu tertentu untuk mencegah pembatalan, terutama selama musim dengan tingkat pembatalan tinggi.
- **Promosi yang Tepat Sasaran**:
  - Menggunakan diskon dan kampanye pemasaran pada bulan dengan tingkat pembatalan tinggi seperti Januari untuk meningkatkan okupansi.
- **Optimalisasi Kanal Pemesanan**:
  - Mendorong pelanggan untuk memesan melalui kanal yang memiliki tingkat pembatalan lebih rendah, seperti reservasi langsung.

### 5.5 Keterbatasan Analisis dan Rekomendasi Pengembangan
1. **Keterbatasan**:
   - **Cakupan Data**: Dataset hanya mencakup dua jenis hotel dan mungkin tidak merepresentasikan semua hotel di lokasi geografis berbeda.
   - **Faktor Eksternal**: Faktor-faktor seperti kondisi ekonomi, cuaca, atau kejadian tak terduga selama periode analisis tidak disertakan.
   - **Kekayaan Data**: Beberapa variabel memiliki data hilang yang signifikan (misalnya, agen pemesanan), sehingga membatasi analisis pada dimensi tersebut.

2. **Rekomendasi Pengembangan**:
   - **Penambahan Variabel Eksternal**: Sertakan data tentang musim, cuaca, atau acara besar untuk memperkaya analisis.
   - **Analisis Lebih Lanjut**: Gunakan model prediktif untuk mengidentifikasi kemungkinan pembatalan berdasarkan karakteristik reservasi.
   - **Data yang Lebih Luas**: Kumpulkan data dari lebih banyak hotel di lokasi geografis berbeda untuk memperluas generalisasi temuan.

Dengan mengatasi keterbatasan ini, analisis di masa depan dapat memberikan wawasan yang lebih kuat dan actionable bagi industri perhotelan.
