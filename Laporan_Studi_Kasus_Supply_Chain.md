# Studi Kasus Analisis End-to-End Supply Chain: Optimalisasi Inventori, Reliabilitas SLA Logistik, dan Profitabilitas DataCo Global

**Disusun oleh:** Bima Sena  
**Cakupan Data:** 180.519 baris transaksi pesanan (Januari 2015 s.d. Januari 2018) & 469.977 log akses katalog web (September 2017 s.d. Januari 2018)  
**Wilayah Operasional:** 5 Market Global, 23 Region, 164 Negara Tujuan, 118 SKU Produk

---

## 1. Konteks Bisnis & Tujuan Analisis

DataCo Global melayani puluhan ribu pesanan lintas negara dengan portofolio 118 produk di 11 departemen. Meskipun perusahaan membukukan penjualan kotor sebesar **$36,78 juta** (penjualan bersih **$33,05 juta** setelah diskon), evaluasi awal terhadap data operasional memperlihatkan empat gejala krusial di sepanjang rantai pasok:

1. **Rendahnya ketepatan waktu pengiriman:** Lebih dari separuh pengiriman aktif terlambat dari jadwal yang dijanjikan kepada pelanggan.
2. **Penurunan tajam pendapatan di akhir periode:** Mulai kuartal IV 2017 hingga Januari 2018, nilai penjualan bulanan turun drastis meskipun jumlah pesanan pelanggan meningkat.
3. **Tergerusnya laba bersih pada tingkat transaksi:** Hampir satu dari lima baris pesanan mencatatkan laba negatif yang memotong laba perusahaan hampir separuhnya.
4. **Risiko pembatalan dan indikasi kecurangan pembayaran:** Ribuan pesanan gagal terkirim akibat pembatalan dan status *Suspected Fraud*.

Analisis ini disusun secara bertahap mulai dari preservasi data mentah (*raw data*), pembersihan dan penataan *Master Data*, penyusunan *Scorecard KPI* berbasis kerangka SCOR (*Supply Chain Operations Reference*), pemetaan inventori **ABC-XYZ**, integrasi trafik web untuk mendeteksi *stockout*, hingga perancangan dashboard dan rekomendasi perbaikan operasional.

---

## 2. Tahapan Pengerjaan & Audit Kualitas Data

Seluruh tahapan kerja didokumentasikan secara berurutan di dalam file kerja `Analisis_Supply_Chain_DataCo.xlsx` tanpa mengubah file sumber asli:

1. **`00_Alur_&_Ringkasan`**: Dokumentasi alur analisis, catatan pembersihan data, dan matriks rekomendasi prioritas.
2. **`01_Raw_Data`**: Menyimpan seluruh **180.519 baris x 36 kolom** data mentah apa adanya sebagai acuan verifikasi.
3. **`02_Data_Master_Clean`**: Tabel master bersih sebanyak **180.519 baris** dengan tipe data terstandarisasi dan atribut analitis rantai pasok.
4. **`03_KPI_Supply_Chain`**: Perhitungan 16 KPI utama rantai pasok, perbandingan tahunan (2015–2018), dan tren historis 37 bulan.
5. **`04_Analisis_Produk_ABC_XYZ`**: Evaluasi menyeluruh 118 SKU produk, klasifikasi Pareto ABC dan volatilitas permintaan XYZ, siklus hidup katalog, serta uji statistik kebocoran margin.
6. **`05_Demand_vs_Stockout`**: Penggabungan 469.977 baris `tokenized_access_logs.csv` dengan realisasi pesanan ERP untuk menghitung potensi penjualan yang hilang akibat kekosongan stok.
7. **`06_Logistik_&_SLA_Delivery`**: Bedah performa *Lead Time*, *OTIF*, dan distribusi hari pengiriman aktual per metode pengiriman.
8. **`07_Market_Regional_&_Risiko`**: Evaluasi 5 pasar global, 15 negara terbesar, ulasan khusus pasar Indonesia, serta audit risiko kanal pembayaran.
9. **`08_Dashboard_Utama`**: Visualisasi ringkas berupa 6 kartu indikator utama dan 6 grafik terintegrasi.

### Temuan Audit & Tindakan Pembersihan Data

Sebelum masuk ke tahap perhitungan KPI, ditemukan enam masalah struktural pada data mentah yang telah diselesaikan pada `02_Data_Master_Clean`:

| No | Temuan pada Data Mentah | Dampak Analitis | Tindakan Standarisasi pada Data Master |
| :-: | :--- | :--- | :--- |
| 1 | Pemisah kolom titik-koma (`;`) dan desimal koma (`,`) | Kolom nilai uang dan persentase terbaca sebagai teks pada pengaturan standar | Konversi penuh ke tipe numerik *float* dan *integer* presisi |
| 2 | Duplikasi kolom bernilai 100% identik (`Benefit per order` = `Order Profit Per Order`; `Sales per customer` = `Order Item Total`) | Berisiko memicu salah tafsir definisi atau perhitungan ganda | Penataan ulang ke atribut tunggal `Net_Profit_USD` dan `Net_Sales_USD` |
| 3 | Penulisan lokasi berbahasa Spanyol & spasi ganda (`EE. UU.`, `Estados Unidos`, `Yakarta`, `South of  USA`) | Agregasi per negara/kota terpecah dan tidak standar secara internasional | Standarisasi seluruh 164 nama negara ke Bahasa Inggris serta normalisasi nama provinsi dan kota di Indonesia |
| 4 | Tercampurnya 7.754 baris pesanan batal (`CANCELED`) dan indikasi kecurangan (`SUSPECTED_FRAUD`) ke metrik pengiriman | Menurunkan persentase keterlambatan secara semu karena pesanan batal diberi label tidak terlambat (`0`) | Pemisahan tegas pengiriman aktif (`Valid_Fulfillment_KPI = 1`, sebanyak 172.765 baris) untuk perhitungan OTIF & SLA |
| 5 | Kolom `Product Status` bernilai `0` pada seluruh 180.519 baris | Sistem ERP seolah menunjukkan stok barang selalu tersedia 100% sepanjang waktu | Validasi silang ketersediaan riil menggunakan rentang bulan aktif produk dan log kunjungan website |
| 6 | Kolom `Margin` mentah dihitung terhadap harga kotor sebelum diskon | Rasio margin bersih tidak mencerminkan potongan diskon hingga 25% | Perhitungan ulang `Net_Margin_Pct` = `Net_Profit_USD / Net_Sales_USD` |

---

## 3. Ringkasan Scorecard KPI Utama (2015–2018)

| Pilar SCOR | Indikator Kinerja Utama (KPI) | Nilai Aktual | Catatan Evaluasi |
| :--- | :--- | ---: | :--- |
| **Komersial & Volume** | Total Pesanan Unik (*Unique Orders*) | 65.752 | Rata-rata ~1.740 order/bulan (2015–Sep 2017), naik ke >2.100 di Q4 2017 |
| **Komersial & Volume** | Total Baris Item & Total Unit Terjual | 180.519 item (384.079 unit) | Rata-rata *Basket Size* 2,75 item/order (5,84 unit/order) |
| **Komersial & Volume** | *Gross Sales* vs *Net Sales* | $36.784.735 vs $33.054.402 | Total potongan diskon mencapai $3.730.333 (10,14% dari *Gross Sales*) |
| **Profitabilitas** | Laba Kotor pada Transaksi Positif | +$7.850.450 | Dihasilkan dari 146.735 baris pesanan sehat (81,29% transaksi) |
| **Profitabilitas** | Kebocoran Kerugian (*Loss Leakage*) | -$3.883.547 | Berasal dari 33.784 baris pesanan merugi (18,71% transaksi) |
| **Profitabilitas** | *Net Profit* Akhir & *Net Profit Margin* | $3.966.903 (12,00%) | Laba bersih terpotong 49,47% akibat transaksi merugi |
| **Reliabilitas Logistik** | Pengiriman Tepat Waktu / Lebih Cepat (*Active OTIF*) | 42,71% (73.788 item) | Jauh di bawah standar industri rantai pasok global (>= 90%) |
| **Reliabilitas Logistik** | Tingkat Keterlambatan Pengiriman Aktif | 57,29% (98.977 item) | Rata-rata waktu kirim aktual 3,50 hari vs target jadwal 2,93 hari (+0,57 hari) |
| **Risiko Order-to-Cash** | Eksposur Pembatalan & *Suspected Fraud* | $1.409.737 (7.754 item) | Mencakup 4,30% total item (4.062 *Fraud* + 3.692 *Canceled*) |

---

## 4. Analisis Mendalam Per Area Supply Chain

### 4.1. Portofolio Produk, Matriks ABC-XYZ, & Anomali Penurunan Omzet Q4 2017

Evaluasi terhadap seluruh 118 SKU produk mengungkapkan tingkat ketergantungan yang sangat tinggi pada segelintir produk utama (*Extreme Pareto Concentration*):

* **Kelas AX (8 SKU / 6,8% Katalog = $28,01 Juta atau 84,73% Total Net Sales):** Delapan produk unggulan berpermintaan stabil (*Coefficient of Variation* ~0,35) menjadi penopang utama perusahaan, dipimpin oleh *Field & Stream Sportsman 16 Gun Fire Safe* ($6,23 juta / 18,84%), *Perfect Fitness Perfect Rip Deck* ($3,97 juta / 12,02%), *Diamondback Women's Serene Classic Comfort Bike* ($3,70 juta / 11,20%), dan *Nike Men's Free 5.0+ Running Shoe* ($3,30 juta / 9,97%).
* **Kelas BX, BY, BZ (15 SKU / 12,7% Katalog = $3,38 Juta atau 10,23% Net Sales):** Dipimpin oleh *Under Armour Girls' Toddler Spine Surge Running Shoe* ($1,14 juta, Kelas BX) serta produk-produk elektronik/rumah tangga baru yang baru masuk pada kuartal IV 2017 seperti *Dell Laptop* ($595,4 ribu) dan *Web Camera* ($240,5 ribu).
* **Kelas CY & CZ (95 SKU / 80,5% Katalog = $1,67 Juta atau 5,04% Net Sales):** Lebih dari empat perlima jumlah SKU hanya menyumbang 5% pendapatan dengan pola permintaan sporadis, memperbesar biaya penyimpanan dan kompleksitas pengelolaan gudang.

**Mengapa Pendapatan Jatuh 71% Saat Jumlah Pesanan Naik?**  
Selama 33 bulan pertama (Januari 2015 – September 2017), kinerja penjualan sangat konsisten di kisaran **$900 ribu hingga $1,03 juta per bulan** dengan rata-rata **3,01 item per pesanan**. Namun tepat setelah Oktober 2017, **seluruh 8 SKU Kelas AX dan 1 SKU Kelas BX berhenti tercatat dalam pemenuhan pesanan** (departemen *Golf* dan *Footwear* berhenti total setelah Oktober 2017). Perusahaan beralih menjual 31 SKU baru (*Technology, Discs Shop, Health and Beauty, Pet Shop, Book Shop*), tetapi pelanggan hanya membeli rata-rata **1,00 item per pesanan** pada November 2017 – Januari 2018. Akibat hilangnya produk inti dan runtuhnya ukuran keranjang belanja, *Net Sales* bulanan merosot dari **$1.027.112 (September 2017)** menjadi **$297.952 (Januari 2018)** walaupun jumlah pesanan unik bertambah dari 1.723 menjadi 2.123 pesanan.

---

### 4.2. Omnichannel Demand Sensing: Bukti Kekosongan Stok dari Log Akses Web

Untuk menguji apakah penurunan penjualan produk olahraga lama disebabkan oleh hilangnya minat konsumen atau masalah ketersediaan barang (*stockout*), dilakukan pencocokan antara **469.977 baris log kunjungan web (`tokenized_access_logs.csv`)** dan transaksi ERP pada rentang waktu yang sama persis (**1 September 2017 – 31 Januari 2018**):

| Kategori Pemenuhan di ERP (Sep 2017 – Jan 2018) | Jumlah SKU di Web | Total Pageviews | Klik Add-to-Cart | Item Terjual di ERP | Konversi Cart-ke-Order |
| :--- | ---: | ---: | ---: | ---: | ---: |
| **Active Fulfillment** (Stok Normal) | 14 SKU | 196.955 | 55.437 | 5.383 | **9,71%** |
| **Near-Stockout** (< 20 Item Terjual) | 22 SKU | 110.979 | 31.344 | 132 | **0,42%** |
| **Phantom Stockout** (0 Order di ERP) | 40 SKU | 162.043 | 46.139 | 0 | **0,00%** |
| **Total Katalog Web** | **76 SKU** | **469.977** | **132.920** | **5.515** | **4,15%** |

Temuan ini membuktikan adanya ketidaksinkronan serius antara sistem inventori gudang dan tampilan situs e-commerce:
1. Sebanyak **62 dari 76 produk yang tayang di website** mengalami kekosongan stok (*Phantom Stockout*) atau stok kritis (*Near-Stockout*), namun tetap dapat diakses pelanggan dan mengumpulkan **77.483 klik tombol *Add-to-Cart***.
2. Produk seperti *adidas Kids' RG III Mid Football Cleat* mencatat **26.200 kunjungan** dan **7.557 klik keranjang** dengan **0 realisasi pesanan**. Demikian pula *Under Armour Hustle Storm Medium Duffle Bag* (**3.841 klik keranjang, 0 pesanan** — terakhir dipasok April 2017) dan *Columbia Men's PFG Anchor Tough T-Shirt* (**3.790 klik keranjang, 0 pesanan**).
3. Menggunakan acuan tingkat konversi normal pada produk yang stoknya tersedia (**9,71%** dari klik keranjang menjadi pesanan), kegagalan penyediaan stok dan sinkronisasi katalog pada 62 SKU ini menghilangkan potensi sekitar **7.390 item pesanan** atau estimasi pendapatan sekitar **$485 ribu** hanya dalam waktu lima bulan.

---

### 4.3. Paradoks SLA Logistik: Mengapa Pengiriman Ekspres Justru Gagal Tepat Waktu?

Pada tingkat agregat, **57,29% pengiriman aktif** mengalami keterlambatan. Pemeriksaan silang antara metode pengiriman (*Shipping Mode*) dan pasar tujuan membuktikan bahwa tingkat keterlambatan hampir seragam di seluruh dunia (Eropa 57,6%, Asia Pasifik 57,4%, Amerika Utara 57,3%, Afrika 57,1%, Amerika Latin 56,8%). Artinya, keterlambatan **bukan disebabkan oleh hambatan geografis wilayah tertentu**, melainkan bersumber dari struktur layanan pengiriman itu sendiri:

| Mode Pengiriman | Komitmen SLA | Aktual Rata-Rata | Selisih Hari | Distribusi Hari Aktual (Pengiriman Aktif) | OTIF Aktif % | Terlambat Aktif % |
| :--- | :-: | :-: | :-: | :--- | ---: | ---: |
| **Standard Class** | <= 4 Hari | 3,99 Hari | -0,01 Hari | Merata di 2, 3, 4, 5, 6 hari (~20% per hari) | **60,23%** | 39,77% |
| **Same Day** | 0 Hari | 0,48 Hari | +0,48 Hari | 52,1% selesai 0 hari; 47,9% bergeser ke 1 hari | **52,07%** | 47,93% |
| **Second Class** | <= 2 Hari | 3,99 Hari | +1,99 Hari | Merata di 2, 3, 4, 5, 6 hari (~20% per hari) | **20,17%** | **79,83%** |
| **First Class** | <= 1 Hari | 2,00 Hari | +1,00 Hari | **100% selesai tepat di hari ke-2** (26.513 item) | **0,00%** | **100,00%** |

Tiga akar masalah operasional teridentifikasi secara pasti dari tabel distribusi di atas:
1. **Ketiadaan Jalur Prioritas untuk Second Class:** Waktu pengiriman aktual *Second Class* (sebaran merata 2 s.d. 6 hari dengan rata-rata **3,99 hari**) **identik persis** dengan *Standard Class* (**3,99 hari**). Pesanan *Second Class* diproses dalam antrean gudang dan transportasi reguler yang sama tanpa percepatan fisik apa pun, sehingga 79,83% pesanan otomatis melanggar janji 2 hari.
2. **Batas Minimum Fisik 48 Jam pada First Class:** Dari 26.513 pengiriman aktif *First Class*, tidak ada satu pun pesanan yang selesai dalam 0 atau 1 hari; seluruhnya membutuhkan tepat 2 hari. Menjanjikan SLA 1 hari tanpa dukungan *cutoff* pengiriman udara/lokal instan menghasilkan kegagalan SLA 100%.
3. **Keterlambatan Cutoff Harian pada Same Day:** Hampir separuh pesanan *Same Day* (47,93%) baru terkirim keesokan harinya karena pesanan masuk melewati batas waktu keberangkatan kurir harian tanpa pembatasan jam pemesanan di halaman *checkout*.

---

### 4.4. Kebocoran Unit Economics (-$3,88 Juta), Pasar Indonesia, & Risiko Pembayaran

1. **Uji Korelasi Diskon vs Kerugian Transaksi:**  
   Dari 180.519 baris pesanan, sebanyak **33.784 baris (18,71%)** mencatatkan laba bersih negatif dengan total kerugian **-$3.883.547**. Pengujian korelasi antara persentase diskon (`Order Item Discount Rate`) dan margin laba bersih menghasilkan angka **r = -0,0027** (tidak berkorelasi). Pada kelompok pesanan **tanpa diskon (0%)**, porsi transaksi merugi sudah mencapai **18,38%**, hampir tidak berbeda dengan kelompok diskon tertinggi **21%–25%** (**19,03%**). Hal ini membuktikan bahwa kerugian per pesanan disebabkan oleh pembebanan biaya pemenuhan tidak proporsional (seperti biaya retur, penalti keterlambatan, atau alokasi ongkos kirim pada pesanan bernilai kecil), meskipun kebijakan diskon hingga 25% tetap perlu diperketat karena menguras potongan pendapatan sebesar **$3,73 juta**.

2. **Posisi Strategis Pasar Indonesia di Kancah Global & ASEAN:**  
   Setelah nama negara berbahasa Spanyol distandarisasi, **Indonesia menempati peringkat ke-11 pasar terbesar dunia sekaligus peringkat ke-1 di Asia Tenggara**, melampaui Filipina ($378,6 ribu), Thailand ($171,5 ribu), Vietnam ($149,4 ribu), Malaysia ($95,8 ribu), dan Singapura ($84,0 ribu). Indonesia menyumbang **1.920 pesanan (4.204 baris item)** dengan *Net Sales* **$761.163** dan *Net Profit* **$90.714** (margin 11,92%).  
   Namun pembedahan tingkat kota di Indonesia memperlihatkan anomali penting: sementara **Jakarta** membukukan penjualan tertinggi ($184.536; laba +$26.415) diikuti **Surabaya** ($74.388) dan **Medan** ($61.081), pengiriman ke **Bekasi** ($37.063 penjualan) dan **Depok, Jawa Barat** ($24.948 penjualan) justru mencetak **akumulasi laba bersih negatif (-$36,89 dan -$71,72)** dengan tingkat keterlambatan pengiriman aktif di Depok mencapai **66,4%**.

3. **Konsentrasi 100% Risiko Fraud & Pembatalan pada Metode TRANSFER:**  
   Pemetaan silang antara metode pembayaran (`DEBIT`, `TRANSFER`, `PAYMENT`, `CASH`) dan status pesanan mengungkap temuan yang sangat tegas: **100% pesanan `SUSPECTED_FRAUD` (4.062 item senilai $741.492) dan 100% pesanan `CANCELED` (3.692 item senilai $668.245) berasal eksklusif dari metode pembayaran `TRANSFER`**. Tidak ditemukan satu pun kasus *fraud* atau pembatalan pada kanal `DEBIT`, `PAYMENT`, maupun `CASH`. Selain itu, terdapat **$7,29 juta** nilai pesanan pada metode `PAYMENT` yang masih menggantung di status `PENDING_PAYMENT` dan menghambat perputaran kas (*Order-to-Cash*).

---

## 5. Rekomendasi Strategis & Rencana Tindak Lanjut

| Prioritas | Fokus Perbaikan | Tindakan Operasional yang Direkomendasikan | Target Indikator & Estimasi Dampak |
| :-: | :--- | :--- | :--- |
| **Prioritas 1** | **Kalibrasi SLA & Jalur Cepat Gudang** | 1. Sesuaikan janji pengiriman di halaman *checkout* agar realistis dengan kapasitas fisik (*First Class*: 2 hari, *Second Class*: 3 hari) sambil membangun jalur *wave-picking* khusus ekspres dengan batas jam pesanan (*cutoff*) pukul 13.00.<br>2. Pisahkan kontrak SLA mitra kurir 3PL antara layanan ekspres dan reguler agar *Second Class* tidak lagi digabung ke rute 4 hari *Standard Class*. | Menurunkan tingkat keterlambatan aktif dari **57,3% ke < 18%** (OTIF naik ke **> 82%**) dan memulihkan kepercayaan pelanggan pada 60.319 pengiriman ekspres. |
| **Prioritas 2** | **Restock SKU Kelas AX & Sinkronisasi Stok Web-ERP** | 1. Pulihkan pasokan 8 SKU Kelas AX dan 1 SKU Kelas BX menggunakan skema *Continuous Review (Reorder Point + Safety Stock 99%)* serta kontrak pemasok berlapis.<br>2. Perbaiki integrasi bendera `Product Status` antara ERP dan situs e-commerce secara *real-time* agar SKU kosong otomatis disembunyikan atau dialihkan ke fitur *Pre-Order / Back-in-Stock Notification*.<br>3. Lakukan rasionalisasi pada 53 SKU Kelas CZ yang berkinerja sangat rendah. | Memulihkan *Basket Size* kembali ke **> 2,5 item/order**, mengangkat omzet bulanan kembali ke kisaran **$850K–$1,0M/bulan**, serta menyelamatkan potensi *lost sales* web **~$485K per 5 bulan**. |
| **Prioritas 3** | **Pengendalian Cost-to-Serve & Margin Floor** | 1. Audit struktur pembebanan biaya logistik, biaya pengiriman ulang, dan retur pada 33.784 baris transaksi yang mencetak margin negatif (khususnya rute dengan biaya *last-mile* tinggi termasuk evaluasi rute Bekasi & Depok di pasar Indonesia).<br>2. Terapkan syarat nilai pesanan minimum (*minimum order threshold*) untuk subsidi ongkos kirim serta batasi plafon diskon promosi maksimal **15%**. | Menekan rasio baris pesanan merugi dari **18,7% menjadi < 5,0%**, dengan potensi pemulihan laba bersih **+$1,5 juta s.d. +$2,5 juta**. |
| **Prioritas 4** | **Mitigasi Fraud TRANSFER & Percepatan Kas** | 1. Terapkan verifikasi pembayaran otomatis (*virtual account / escrow gateway*) pada kanal `TRANSFER` sebelum pesanan diteruskan ke antrean gudang.<br>2. Terapkan batas waktu pembayaran otomatis (*auto-expire* 24 jam) untuk mencairkan penumpukan status `PENDING_PAYMENT` ($7,29 juta). | Menekan tingkat pembatalan & *fraud* pada kanal `TRANSFER` dari **15,5% ke < 2,0%**, mengamankan eksposur barang senilai **$1,41 juta**. |

---

## 6. Daftar File Hasil Kerja Portofolio

1. **`Analisis_Supply_Chain_DataCo.xlsx`** — Workbook Excel lengkap 9 *sheet* berisi alur kerja bertahap mulai dari *Raw Data* (180.519 baris), *Data Master Clean* (180.519 baris), *Scorecard KPI*, Analisis Produk *ABC-XYZ* (118 SKU), *Demand vs Stockout* (76 SKU Web), Audit Logistik & SLA, Analisis Geografis & Indonesia, hingga *Dashboard Utama* dengan rumus Excel aktif dan grafik terintegrasi.
2. **`Dashboard_Supply_Chain_DataCo.html`** — Dashboard analitis interaktif mandiri (dapat dibuka langsung melalui browser tanpa koneksi internet) dengan fitur filter lintas Tahun dan Market, pencarian 118 SKU produk, matriks hari pengiriman aktual, serta evaluasi pasar Indonesia dan risiko pembayaran.
3. **`Laporan_Studi_Kasus_Supply_Chain.md`** — Dokumen narasi analisis mendalam, metodologi pembersihan data, interpretasi temuan bisnis, dan rekomendasi strategis rantai pasok.
