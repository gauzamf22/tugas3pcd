# PCD Assignment 03: Image Segmentation and Morphological Processing

* **Nama:** Muhammad Gauza Faliha
* **NIM:** 25/555851/PA/23315
* **Prodi:** Ilmu Komputer
* **Mata Kuliah:** Pengolahan Citra Digital

Pada repositori ini saya mengimplementasikan metode segmentasi citra menggunakan Otsu Thresholding dan operasi morfologi matematika mencakup Erosi, Dilasi, Opening, dan Closing dari nol tanpa library pengolahan citra pada citra sidik jari, kemudian membandingkan hasil segmentasi sebelum dan sesudah penerapan pemrosesan morfologi.

## Arsitektur Pipeline Sistem

Berikut adalah arsitektur sistem pengolahan citra digital untuk segmentasi dan operasi morfologi matematika yang dibangun dari nol tanpa library pengolahan citra:

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
               |           TAHAP 1: PREPROCESSING KEABUAN                    |
               |          Konversi Luminansi: Y = 0.299R + 0.587G + 0.114B   |
               |          Normalisasi Matriks Intensitas Piksel (0 s/d 255)  |
               +-------------------------------------------------------------+
                                              |
                                              v
               +-------------------------------------------------------------+
               |        TAHAP 2: SEGMENTASI OTSU THRESHOLDING                |
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
  |   HASIL BASELINE TANPA MORFOLOGI    |   |     TAHAP 3: OPERASI MORFOLOGI      |
  |  * Garis alur terputus putus        |   |  * Elemen Penstruktur Palang (3x3)  |
  |  * Lubang pori di dalam alur        |   |  * Mekanisme Zero Padding Batas     |
  |  * Bintik kotoran latar belakang    |   +-------------------------------------+
  +-------------------------------------+                              |
                     |                                                 v
                     |                      +-------------------------------------+
                     |                      |      OPERATOR MORFOLOGI DARI NOL    |
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
               |     Penelusuran antrean BFS dari nol untuk mengukur         |
               |     penyambungan alur alur yang terputus                    |
               +-------------------------------------------------------------+
```

## Pembahasan Terstruktur Per Bagian

1. **Pemuatan Citra dan Konversi Keabuan**: Pada tahap ini saya membaca berkas citra sidik jari 100__M_Left_index_finger.bmp yang berukuran 96 x 103 piksel. Di sini saya memastikan format citra berupa grayscale satu kanal. Apabila citra masukan bertipe tiga kanal warna RGB, saya mengonversinya secara manual menggunakan bobot luminansi standar NTSC yaitu Y = 0.299 R + 0.587 G + 0.114 B. Pada citra ini, alur alur sidik jari memiliki intensitas rendah mendekati hitam sedangkan latar belakang memiliki intensitas tinggi mendekati putih.

2. **Perhitungan Histogram Citra**: Setelah citra berhasil dimuat, langkah berikutnya yang saya lakukan adalah menghitung histogram distribusi frekuensi kemunculan intensitas piksel dari nilai 0 hingga 255. Nilai probabilitas tiap intensitas dihitung dengan membagi frekuensi kemunculan piksel dengan total seluruh piksel. Grafik histogram ini memperlihatkan kurva bimodal yang jelas antara area alur sidik jari yang gelap dan latar belakang yang terang, yang nantinya menjadi dasar penentuan nilai ambang batas pada metode Otsu.

3. **Segmentasi Citra Menggunakan Metode Otsu**: Di sini saya menerapkan segmentasi citra menggunakan metode Otsu Thresholding dari nol. Algoritma ini mencari nilai ambang batas T optimal secara otomatis dengan menguji seluruh kandidat nilai intensitas dan memilih nilai T yang menghasilkan Within Class Variance terendah. Titik minimum global diperoleh pada nilai T = 131. Kemudian saya melakukan binerisasi dengan membalik nilai polaritas agar alur alur sidik jari ditetapkan bernilai satu sebagai objek utama dan latar belakang bernilai nol.

4. **Analisis Hasil Segmentasi Tanpa Morfologi**: Di sini saya menampilkan citra biner mentah hasil segmentasi Otsu yang menjadi baseline pembanding tanpa penerapan operasi morfologi. Karena segmentasi berbasis piksel murni hanya melihat nilai intensitas keabuan tanpa memperhatikan keterhubungan bentuk spasial, kita dapat melihat bahwa alur alur sidik jari masih banyak yang terputus putus akibat tekanan sensor yang tidak merata, terdapat lubang pori putih di tengah alur alur, serta muncul bintik bintik kotoran pada latar belakang.

5. **Implementasi Operator Morfologi Dari Nol**: Untuk memperbaiki cacat struktural pada citra biner tersebut, di sini saya mengimplementasikan fungsi zero padding serta empat operator morfologi matematika dasar dari nol:
* Erosi: Mengikis batas luar objek, di mana piksel keluaran bernilai satu hanya jika seluruh elemen penstruktur cocok sempurna dengan piksel objek di bawahnya.
* Dilasi: Memperluas batas luar objek, di mana piksel keluaran bernilai satu jika minimal ada satu elemen penstruktur yang mengenai piksel objek di bawahnya.
* Opening: Proses erosi yang dilanjutkan dengan dilasi untuk membersihkan tonjolan kecil serta bintik kotoran pada latar belakang tanpa merusak bentuk alur utama.
* Closing: Proses dilasi yang dilanjutkan dengan erosi untuk menyambungkan celah alur alur yang terputus dan menutup lubang pori kecil di dalam alur.
* Ekstraksi Kontur Batas: Mengambil batas tepi alur dengan mengurangkan citra biner asli dengan citra hasil erosi.

6. **Penerapan Operasi Morfologi Pada Citra Sidik Jari**: Setelah fungsi fungsi morfologi siap, di sini saya menerapkan seluruh operator tersebut pada citra biner sidik jari. Saya memilih elemen penstruktur bentuk palang cross berukuran 3x3 piksel agar keterhubungan alur tersambung baik pada arah horizontal maupun vertikal tanpa menimbulkan pelebaran diagonal berlebih yang dapat menyatukan dua alur bersebelahan. Selain menjalankan operator tunggal, saya juga membuat pipeline kombinasi yaitu operasi Closing untuk menyambung alur yang kemudian dilanjutkan dengan operasi Opening untuk membersihkan sisa bintik kotoran pada latar belakang.

7. **Evaluasi Kuantitatif Komponen Terhubung**: Untuk membuktikan efektivitas pemrosesan morfologi secara objektif dan terukur, di sini saya mengimplementasikan algoritma Connected Component Labeling dari nol menggunakan penelusuran Breadth First Search berbasis antrean. Melalui algoritma ini saya menghitung banyaknya potongan alur yang terpisah sebelum dan sesudah operasi morfologi. Alur yang terputus putus akan terhitung sebagai banyak komponen terpisah, sedangkan alur yang berhasil tersambung kembali akan menyatu menjadi komponen kontinu sehingga jumlah total komponen akan menurun secara signifikan.

## Hasil Analisis Histogram dan Ambang Batas Otsu

Grafik distribusi intensitas keabuan citra sidik jari dan kurva Within Class Variance yang mencapai nilai minimum pada ambang batas T = 131:

![Analisis Otsu dan Histogram](analisis_otsu_histogram.png)

## Hasil Komparasi Sebelum dan Sesudah Morfologi

Perbandingan antara hasil segmentasi tanpa pemrosesan morfologi dengan hasil segmentasi setelah diterapkan operasi Closing:

![Perbandingan Sebelum dan Sesudah Morfologi](hasil_sebelum_dan_sesudah.png)

## Hasil Seluruh Operator Morfologi

Visualisasi komparasi delapan panel mencakup citra asli, tanpa morfologi, erosi, dilasi, opening, closing, pipeline kombinasi, serta ekstraksi kontur batas alur:

![Hasil Perbandingan Lengkap](hasil_perbandingan_lengkap.png)

## Hasil Evaluasi Kuantitatif Komparasi

Evaluasi dilakukan menggunakan algoritma Connected Component Labeling berbasis penelusuran antrean BFS untuk mengukur tingkat fragmentasi alur:

| Tahapan Pemrosesan Citra | Jumlah Komponen Terpisah | Keterangan Struktur Garis Alur |
| :--- | :--- | :--- |
| Segmentasi Tanpa Morfologi (Otsu Raw) | 163 komponen | Alur terputus putus dan terdapat bintik kotoran |
| Segmentasi Dengan Morfologi Closing | 47 komponen | Alur tersambung dan lubang pori tertutup |
| Segmentasi Dengan Pipeline Closing dan Opening | 45 komponen | Alur tersambung dan latar belakang bersih |

Persentase pengurangan serpihan alur yang terputus mencapai 71.17 persen, yang menunjukkan keberhasilan operasi Closing dalam menyambungkan kembali alur sidik jari.

## Kesimpulan

Berdasarkan keseluruhan tahapan eksperimen yang telah saya lakukan pada citra sidik jari 100__M_Left_index_finger.bmp, dapat ditarik beberapa kesimpulan penting sebagai berikut:

1. Evaluasi Metode Segmentasi Otsu Thresholding:
Metode Otsu berhasil menentukan nilai ambang batas optimal T = 131 secara otomatis berdasarkan minimasi variansi dalam kelas Within Class Variance. Namun, karena segmentasi berbasis piksel hanya mempertimbangkan distribusi intensitas keabuan tanpa memperhatikan keterhubungan spasial, citra biner mentah yang dihasilkan memiliki keterbatasan struktural berupa alur sidik jari yang terputus putus, lubang pori keringat di dalam alur, serta sebaran bintik kotoran pada latar belakang.

2. Analisis Peran Operator Morfologi:
* Operasi Erosi mengikis batas luar objek dan memperlebar celah alur yang terputus, sehingga tidak cocok digunakan secara tunggal pada citra alur sidik jari yang tipis.
* Operasi Dilasi mampu menjembatani alur yang terputus dan menutup lubang pori, namun menyebabkan garis alur menjadi lebih tebal yang berisiko menggabungkan dua alur yang bersebelahan.
* Operasi Opening yang merupakan proses erosi diikuti dilasi terbukti efektif untuk menghilangkan bintik kotoran kecil pada latar belakang tanpa mengubah ukuran garis alur utama.
* Operasi Closing yang merupakan proses dilasi diikuti erosi terbukti menjadi operator paling efektif untuk restorasi citra sidik jari. Dilasi pada tahap awal berhasil menghubungkan kembali alur alur yang terputus dan menutup pori pori kecil, kemudian erosi pada tahap kedua mengembalikan ketebalan alur ke proporsi aslinya.
* Pipeline kombinasi Closing yang dilanjutkan dengan Opening menghasilkan representasi biner terbaik karena alur sidik jari tersambung secara kontinu sekaligus latar belakang bersih dari bintik kotoran.

3. Validasi Objektif Menggunakan Metrik Kuantitatif Komponen Terhubung:
Pengujian kuantitatif menggunakan Connected Component Labeling berbasis penelusuran Breadth First Search menunjukkan bahwa citra hasil segmentasi tanpa morfologi memiliki 163 komponen terpisah akibat tingginya tingkat fragmentasi alur dan gangguan noise. Setelah penerapan operasi Closing, jumlah komponen terpisah berkurang menjadi 47 komponen, yang merepresentasikan tingkat reduksi fragmentasi alur sebesar 71.17 persen. Penurunan jumlah komponen ini membuktikan secara ilmiah bahwa pemrosesan morfologi berhasil menyambungkan serpihan serpihan alur yang terpisah menjadi garis alur yang utuh dan kontinu.
