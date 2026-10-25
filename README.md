# PCD Assignment 03: Image Segmentation and Morphological Processing

* **Nama:** Muhammad Gauza Faliha
* **NIM:** 25/555851/PA/23315
* **Prodi:** Ilmu Komputer
* **Mata Kuliah:** Pengolahan Citra Digital

Pada tugas ini saya mengimplementasikan segmentasi Otsu Thresholding dan operasi morfologi (erosi, dilasi, opening, closing) untuk pengolahan citra pada citra sidik jari, lalu membandingkan hasil segmentasi sebelum dan sesudah morfologi.

File: `PCD_Assignment03.ipynb` (notebook), `100__M_Left_index_finger.bmp` (citra), dan results citra nya.

## Alur Pengerjaan

1. **Input citra**: membaca citra sidik jari dan mengubahnya ke grayscale. Bingkai abu-abu 2 piksel di sisi atas dan kiri saya buang karena bukan bagian sidik jari.
2. **Histogram**: menghitung frekuensi intensitas 0 sampai 255 untuk masukan Otsu.
3. **Otsu**: mencari T dengan within class variance terendah, hasilnya T = 128.
4. **Binerisasi**: nilai dibalik supaya alur sidik jari bernilai 1 dan latar bernilai 0.
5. **Morfologi**: erosi, dilasi, opening, closing, dan kontur, semuanya dibuat dari nol.
6. **Evaluasi**: menghitung komponen terhubung dengan BFS dari nol.

## Hasil

### Histogram dan nilai T Otsu
![Analisis Otsu dan Histogram](analisis_otsu_histogram.png)

### Sebelum dan sesudah morfologi
![Perbandingan Sebelum dan Sesudah Morfologi](hasil_sebelum_dan_sesudah.png)

### Semua operasi morfologi
![Hasil Perbandingan Lengkap](hasil_perbandingan_lengkap.png)

## Evaluasi

Rasio ROI adalah persentase piksel alur di dalam area sidik jari. Kalau angkanya naik jauh dari citra mentah, berarti celah antar alur ikut tertutup.

| Tahap | Komponen (CC-8) | Bintik kecil (<6 px) | Rasio ROI |
| :--- | :--- | :--- | :--- |
| Otsu mentah | 43 | 23 | 57.0% |
| Closing palang 3x3 | 16 | 15 | 89.0% |
| Opening palang 3x3 | 39 | 18 | 26.9% |
| Pipeline mask ROI + hapus bintik | 20 | 0 | 56.4% |

## Kesimpulan

1. **Otsu** dapat memisahkan alur sidik jari dari latar belakang secara otomatis dengan T = 128. Tapi hasil mentahnya masih punya alur yang putus-putus dan bintik di latar. Bagian bawah citra juga masih menyatu karena threshold-nya satu untuk seluruh citra.

2. **Operator tunggal 3x3 palang/kernel kurang cocok** untuk citra ini, karena alur dan celahnya cuma selebar 2 sampai 3 piksel. Erosi dan opening membuang lebih dari separuh piksel alur, sedangkan dilasi dan closing menutup celah antar alur sampai area sidik jari jadi blok putih. Jumlah komponen pada closing memang turun, tapi itu karena alurnya menyatu, bukan karena alur yang putus tersambung.

3. **Pipeline mask ROI** hasilnya paling bagus. Mask dibentuk dengan closing dan opening kotak 7x7, di-AND dengan citra mentah, lalu komponen kecil dihapus. Bintik di luar sidik jari hilang dan rasio alur di dalam ROI hampir sama dengan citra mentah, jadi bentuk alurnya tetap terjaga. Di sini morfologi dipakai untuk menemukan daerah sidik jari, bukan untuk mengubah alurnya.

4. **Keterbatasan:** pipeline ini belum menyambung alur yang putus di dalam sidik jari dan belum memperbaiki bagian bawah yang menyatu. Kemungkinan perbaikannya adalah thresholding lokal (misalnya Otsu per blok), namun hal tersebut belum saya lakukan pada tugas ini karena tugas hanya meminta satu metode segmentasi dan beberapa operasi morfologi, sehingga thresholding lokal berada di luar cakupan.
