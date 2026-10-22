# Dokumentasi Proyek PCD Assignment 03

## Arsitektur Pipeline Sistem

Berikut adalah arsitektur lengkap sistem pengolahan citra digital untuk segmentasi dan operasi morfologi matematika yang dibangun dari nol tanpa library pengolahan citra:

```text
====================================================================================================
                                ARSITEKTUR PIPELINE PCD ASSIGNMENT 03
====================================================================================================

               +-------------------------------------------------------------+
               |                       CITRA MASUKAN                         |
               |                100__M_Left_index_finger.bmp                 |
               |                  Dimensi: 96 x 103 piksel                   |
               +-------------------------------------------------------------+
                                              |
                                              v
               +-------------------------------------------------------------+
               |           TAHAP 1: PREPROCESSING KEABUAN MANDIRI            |
               |          Konversi Luminansi: Y = 0.299R + 0.587G + 0.114B   |
               |          Normalisasi Matriks Intensitas Piksel (0 s/d 255)  |
               +-------------------------------------------------------------+
                                              |
                                              v
               +-------------------------------------------------------------+
               |        TAHAP 2: MESIN SEGMENTASI OTSU THRESHOLDING          |
               |  1. Ekstraksi Distribusi Histogram Frekuensi 256 Tingkat     |
               |  2. Probabilitas Kerapatan Piksel: p(i) = hist(i) / Total   |
               |  3. Iterasi Uji Nilai T dari 0 hingga 255                   |
               |  4. Minimasi Variansi Dalam Kelas: min(w0*var0 + w1*var1)   |
               |  5. Ambang Batas Optimal Terpilih: T = 131                  |
               |  6. Binerisasi Polaritas: Alur Objek = 1, Latar Belakang = 0|
               +-------------------------------------------------------------+
                                              |
                     +------------------------+------------------------+
                     |                                                 |
                     v                                                 v
  +-------------------------------------+   +-------------------------------------+
  |   HASIL BASELINE TANPA MORFOLOGI    |   |     TAHAP 3: MESIN MORFOLOGI        |
  |  * Garis alur terputus putus        |   |  * Elemen Penstruktur Palang (3x3)  |
  |  * Lubang pori di dalam alur        |   |  * Mekanisme Zero Padding Batas     |
  |  * Bintik kotoran latar belakang    |   +-------------------------------------+
  +-------------------------------------+                              |
                     |                                                 v
                     |                      +-------------------------------------+
                     |                      |      OPERATOR MORFOLOGI MANDIRI     |
                     |                      |  1. Erosi (Pengikisan Batas)        |
                     |                      |  2. Dilasi (Penebalan dan Sambung)  |
                     |                      |  3. Opening (Erosi lalu Dilasi)     |
                     |                      |  4. Closing (Dilasi lalu Erosi)     |
                     |                      |  5. Ekstraksi Kontur Batas Tepi     |
                     |                      +-------------------------------------+
                     |                                                 |
                     +------------------------+------------------------+
                                              |
                                              v
               +-------------------------------------------------------------+
               |              TAHAP 4: ANALISIS KOMPARATIF                   |
               |                                                             |
               |  A. Komparasi Visual Berdampingan:                          |
               |     Perbandingan sebelum versus sesudah morfologi           |
               |                                                             |
               |  B. Evaluasi Kuantitatif Connected Component Labeling:      |
               |     Penelusuran antrean BFS dari nol untuk membuktikan      |
               |     penyambungan alur alur yang terputus                    |
               +-------------------------------------------------------------+
```

## Hasil Analisis Histogram dan Ambang Batas Otsu

Grafik di bawah ini menampilkan distribusi intensitas keabuan citra sidik jari serta kurva Within Class Variance dari metode Otsu yang mencapai titik minimum global pada nilai ambang batas T = 131:

![Analisis Otsu dan Histogram](analisis_otsu_histogram.png)

## Hasil Komparasi Utama Sebelum dan Sesudah Morfologi

Perbandingan langsung antara hasil segmentasi tanpa pemrosesan morfologi dengan hasil segmentasi setelah diterapkan operasi morfologi Closing:

![Perbandingan Sebelum dan Sesudah Morfologi](hasil_sebelum_dan_sesudah.png)

## Hasil Lengkap Seluruh Operator Morfologi

Berikut adalah visualisasi menyeluruh delapan panel yang membandingkan citra asli, segmentasi tanpa morfologi, hasil empat operator morfologi dasar, hasil kombinasi pipeline, serta ekstraksi kontur batas:

![Hasil Perbandingan Lengkap](hasil_perbandingan_lengkap.png)

## Tabel Hasil Evaluasi Kuantitatif Komparasi

Evaluasi dilakukan menggunakan algoritma Connected Component Labeling berbasis penelusuran antrean BFS yang dibangun dari nol untuk mengukur tingkat fragmentasi alur:

| Tahapan Pemrosesan Citra | Jumlah Komponen Terpisah | Status Keterhubungan Garis Alur |
| :--- | :--- | :--- |
| Segmentasi Tanpa Morfologi (Otsu Raw) | 163 komponen | Alur terputus putus dan banyak bintik kotoran |
| Segmentasi Dengan Morfologi Closing | 47 komponen | Alur tersambung utuh dan lubang pori tertutup |
| Segmentasi Dengan Pipeline Closing dan Opening | 45 komponen | Alur tersambung rapi dan latar belakang bersih total |

Tingkat reduksi serpihan alur yang terputus mencapai 71.17 persen, membuktikan secara objektif keberhasilan operasi morfologi dalam menyambung alur alur sidik jari.
