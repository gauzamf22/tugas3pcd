# PCD Assignment 03: Image Segmentation and Morphological Processing

* **Nama:** Muhammad Gauza Faliha
* **NIM:** 25/555851/PA/23315
* **Prodi:** Ilmu Komputer
* **Mata Kuliah:** Pengolahan Citra Digital

Pada repositori ini saya mengimplementasikan metode segmentasi citra menggunakan Otsu Thresholding dan operasi morfologi matematika mencakup Erosi, Dilasi, Opening, dan Closing dari nol tanpa library pengolahan citra pada citra sidik jari, kemudian membandingkan hasil segmentasi sebelum dan sesudah penerapan pemrosesan morfologi.

## Alur Pengerjaan

Alur pengerjaan pada notebook ini saya susun sebagai berikut:

Step 1: Input Citra -> Step 2: Hitung Histogram -> Step 3: Segmentasi Otsu -> Step 4: Binerisasi Citra -> Step 5: Operasi Morfologi -> Step 6: Evaluasi Komparatif

1. **Step 1: Input Citra**: Membaca citra sidik jari 100__M_Left_index_finger.bmp dan mengubahnya ke format grayscale.
2. **Step 2: Hitung Histogram**: Menghitung sebaran intensitas piksel 0 hingga 255 sebagai masukan untuk metode Otsu.
3. **Step 3: Segmentasi Otsu**: Menentukan nilai ambang batas optimal T = 131 berdasarkan nilai Within Class Variance terendah.
4. **Step 4: Binerisasi Citra**: Membalik nilai biner agar alur sidik jari bernilai satu dan latar belakang bernilai nol.
5. **Step 5: Operasi Morfologi**: Menerapkan operasi Erosi, Dilasi, Opening, Closing, dan Batas Kontur menggunakan kernel palang 3x3 dari nol.
6. **Step 6: Evaluasi Komparatif**: Membandingkan hasil visual sebelum versus sesudah morfologi dan menghitung pengurangan jumlah komponen terhubung menggunakan penelusuran BFS.

## Hasil Percobaan

### 1. Histogram dan Nilai Ambang Batas Otsu
Grafik sebaran intensitas keabuan dan kurva variansi Otsu dengan nilai ambang batas optimal pada T = 131:

![Analisis Otsu dan Histogram](analisis_otsu_histogram.png)

### 2. Perbandingan Sebelum dan Sesudah Morfologi
Perbandingan langsung antara hasil segmentasi Otsu mentah dengan hasil segmentasi setelah diterapkan operasi Closing:

![Perbandingan Sebelum dan Sesudah Morfologi](hasil_sebelum_dan_sesudah.png)

### 3. Hasil Seluruh Operasi Morfologi
Visualisasi delapan panel yang membandingkan citra asli, segmentasi tanpa morfologi, hasil tiap tiap operator morfologi, pipeline kombinasi, dan ekstraksi batas:

![Hasil Perbandingan Lengkap](hasil_perbandingan_lengkap.png)

## Evaluasi Kuantitatif

Untuk mengukur keterhubungan garis alur secara objektif, saya menghitung jumlah komponen terhubung menggunakan penelusuran Breadth First Search dari nol:

| Tahapan Pemrosesan Citra | Jumlah Komponen Terpisah | Keterangan Alur Sidik Jari |
| :--- | :--- | :--- |
| Segmentasi Tanpa Morfologi (Otsu Raw) | 163 komponen | Alur masih terputus putus dan banyak bintik kotoran |
| Segmentasi Dengan Morfologi Closing | 47 komponen | Alur tersambung kembali dan pori pori tertutup |
| Segmentasi Dengan Pipeline Closing dan Opening | 45 komponen | Alur tersambung rapi dan latar belakang bersih |

Operasi Closing berhasil mengurangi serpihan alur yang terputus sebesar 71.17 persen.

## Kesimpulan

1. Segmentasi Otsu berhasil memisahkan alur sidik jari dari latar belakang secara otomatis pada nilai T = 131, namun hasil biner mentah masih memiliki kelemahan berupa alur alur yang terputus putus, lubang pori pada alur, dan bintik kotoran di latar belakang.
2. Operasi Closing merupakan operasi yang paling cocok untuk citra sidik jari karena berhasil menyambungkan alur alur yang terputus dan menutup pori pori tanpa menyebabkan alur menjadi terlalu tebal.
3. Evaluasi kuantitatif menggunakan penelusuran komponen terhubung membuktikan adanya penurunan potongan alur dari 163 menjadi 47 komponen (reduksi fragmentasi sebesar 71.17 persen).
