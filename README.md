# Analisis End-to-End Supply Chain: Optimalisasi Inventori, Perencanaan Pengadaan, dan Reliabilitas Logistik (DataCo Global)

**Oleh:** Bima Sena  
**Dashboard Interaktif & Chatbot Supply Chain (Live Demo):** [https://bimasena14-code.github.io/supply-chain-analytics-dataco/](https://bimasena14-code.github.io/supply-chain-analytics-dataco/)

---

## Ringkasan Proyek

Proyek ini membedah kinerja rantai pasok global pada **180.519 baris transaksi pesanan** (Januari 2015 – Januari 2018) dan **469.977 baris log kunjungan katalog web** (September 2017 – Januari 2018) di 5 wilayah pasar global, 164 negara tujuan, dan 118 SKU produk.

Seluruh tahapan dikerjakan secara menyeluruh mulai dari preservasi data mentah, pembersihan dan standarisasi *Master Data*, penyusunan *Scorecard KPI* berbasis kerangka **SCOR** (*Plan, Source, Make/Fulfill, Store, Deliver*), pemetaan inventori **Matriks ABC-XYZ**, perhitungan **EOQ, Safety Stock, dan Reorder Point (ROP)**, hingga pembuatan dashboard interaktif yang dilengkapi fitur **Tanya Data (Chatbot MRP & Pengadaan Bulanan)**.

---

## Struktur File Repositori

| Nama File | Deskripsi |
| :--- | :--- |
| `index.html` / `Dashboard_Supply_Chain_DataCo.html` | Dashboard BI interaktif mandiri (5 menu tahapan *Supply Chain*, filter lintas Tahun & Market, simulator *Supply Plan*, kalkulator *EOQ*, matriks *ABC-XYZ* interaktif, serta fitur *Chatbot* simulasi *supply* & pengadaan bulanan). |
| `Analisis_Supply_Chain_DataCo.xlsx` | Workbook Excel lengkap **9 sheet** berurutan dari `01_Raw_Data` (180.519 baris), `02_Data_Master_Clean` (180.519 baris), `03_KPI_Supply_Chain`, `04_Analisis_Produk_ABC_XYZ`, `05_Demand_vs_Stockout`, `06_Logistik_&_SLA_Delivery`, `07_Market_Regional_&_Risiko`, hingga `08_Dashboard_Utama` dengan rumus Excel aktif dan grafik terintegrasi. |
| `Laporan_Studi_Kasus_Supply_Chain.md` | Dokumentasi lengkap metodologi pembersihan data, analisis mendalam per tahapan rantai pasok, dan matriks rekomendasi bisnis. |
| `DescriptionDataCoSupplyChain.csv` | Kamus deskripsi atribut resmi dataset DataCo Global. |

> **Catatan Dataset Mentah:** Dua file CSV mentah berukuran besar (`DataCoSupplyChainDataset.csv` 57,8 MB dan `tokenized_access_logs.csv` 95,4 MB) dikecualikan melalui `.gitignore` agar ukuran repositori tetap efisien. Sumber asli berasal dari *DataCo Smart Supply Chain for Big Data Analysis*, dan seluruh 180.519 baris data mentah maupun data bersih sudah tersimpan utuh di dalam `Analisis_Supply_Chain_DataCo.xlsx` (`01_Raw_Data` dan `02_Data_Master_Clean`).

---

## Scorecard Indikator Utama (2015–2018)

| Area Kinerja | Indikator Utama (KPI) | Nilai Aktual | Catatan Evaluasi |
| :--- | :--- | ---: | :--- |
| **Komersial** | Total Pesanan Unik & Baris Item | 65.752 pesanan (180.519 item) | Rata-rata *Basket Size* 2,75 item/pesanan (384.079 unit terjual) |
| **Komersial** | *Gross Sales* vs *Net Sales* | \$36.784.735 vs \$33.054.402 | Potongan diskon mencapai \$3.730.333 (10,14% dari nilai kotor) |
| **Profitabilitas** | Laba Kotor Transaksi Positif | +\$7.850.450 | Dihasilkan oleh 146.735 baris pesanan sehat (81,29% transaksi) |
| **Profitabilitas** | Kebocoran Transaksi Merugi | -\$3.883.547 | Berasal dari 33.784 baris pesanan minus (18,71% transaksi) |
| **Profitabilitas** | *Net Profit* Akhir & *Net Margin* | \$3.966.903 (12,00%) | Laba bersih terpotong 49,47% akibat beban transaksi merugi |
| **Logistik & SLA** | Ketepatan Pengiriman Aktif (*OTIF*) | 42,71% (73.788 item) | Di bawah standar industri (>= 90%); tingkat terlambat aktif **57,29%** |
| **Risiko Pembayaran** | Eksposur Pembatalan & *Fraud* | \$1.409.737 (7.754 item) | 100% berasal dari kanal pembayaran `TRANSFER` |

---

## Ringkasan Temuan Utama pada 5 Tahap Supply Chain

### 1. Perencanaan (*Demand & Supply Planning*)
* Selama 33 bulan pertama (Januari 2015 – September 2017), permintaan pasar stabil di kisaran **~10.750 unit/bulan (\$930 ribu – \$1,03 juta/bulan)** dengan ukuran keranjang **3,01 item/pesanan**.
* Pencocokan **469.977 log kunjungan web** dengan transaksi ERP pada September 2017 – Januari 2018 membuktikan bahwa penurunan omzet 71% di akhir 2017 bukan disebabkan oleh turunnya minat beli, melainkan kekosongan stok pada **62 dari 76 SKU di website** yang masih menerima **77.483 klik *Add-to-Cart*** tanpa ketersediaan barang (estimasi kehilangan potensi pendapatan **~\$485 ribu** dalam 5 bulan).

### 2. Pengadaan (*Procurement & EOQ*)
* Sebagai distributor ritel barang jadi, DataCo memasok produk dari **32 prinsipal merek** (*Field & Stream, Perfect Fitness, Diamondback, Nike, Pelican, Under Armour, Dell*, dll.) dengan estimasi nilai pengadaan (HPP) sekitar **\$24,13 juta**.
* Pasokan 9 SKU olahraga inti terhenti setelah Oktober 2017. Rencana pemulihan pengadaan telah dihitung menggunakan metode **EOQ (*Economic Order Quantity*)** untuk mengoptimalkan biaya pemesanan dan penyimpanan per *Purchase Order (PO)*.

### 3. Proses Operasional (*Order Fulfillment Operations*)
* Pesanan pelanggan masuk secara merata sepanjang **24 jam penuh**, sedangkan proses internal dari verifikasi pembayaran hingga paket diserahkan ke kurir membutuhkan waktu fisik minimum **48 jam**.
* Seluruh insiden `SUSPECTED_FRAUD` (4.062 item / \$741,5 ribu) dan `CANCELED` (3.692 item / \$668,2 ribu) terkonsentrasi **100% pada metode pembayaran `TRANSFER`**, sementara \$7,29 juta tertahan pada status `PENDING_PAYMENT`.

### 4. Penyimpanan Gudang (*Warehousing & Matriks ABC-XYZ*)
* Pengiriman dilayani dari dua hub gudang asal: **United States** (61,6% volume) dan **Puerto Rico** (38,4% volume).
* Pemetaan **Matriks ABC-XYZ** pada 118 SKU menunjukkan konsentrasi ekstrem: hanya **8 SKU Kelas AX (6,8% katalog)** menopang **\$28,01 juta (84,73% total penjualan)** dengan pola permintaan sangat stabil (*CV ~0,35*). Sebaliknya, **53 SKU Kelas CZ** hanya menyumbang 2,08% omzet dengan fluktuasi tinggi sehingga direkomendasikan dialihkan ke skema *dropship*.

### 5. Distribusi & Pengiriman (*Logistics & Pasar Indonesia*)
* Keterlambatan pengiriman aktif (**57,29%**) seragam di seluruh benua dan berakar pada desain SLA ekspedisi: **First Class** (janji 1 hari) terlambat **100%** karena seluruh paket tiba pada hari ke-2, sedangkan **Second Class** (janji 2 hari) memiliki rata-rata waktu kirim aktual **3,99 hari**, identik persis dengan **Standard Class** (3,99 hari).
* **Pasar Indonesia** menempati **peringkat ke-11 dunia** dan **ke-1 di Asia Tenggara** (**\$761.163** *Net Sales*, **\$90.714** laba bersih dari 1.920 pesanan di 43 kota). Namun evaluasi tingkat kota mengungkap akumulasi rugi bersih pada rute **Depok, Jawa Barat (-\$71,72; terlambat 66,4%)** dan **Bekasi (-\$36,89)**.
* Uji korelasi membuktikan rasio transaksi merugi (18,71% pesanan) tidak dipicu oleh tingkat diskon (*r = -0,0027*), melainkan beban biaya pemenuhan dan retur per pesanan.

---

## Rekomendasi Prioritas

1. **Kalibrasi SLA & Pemisahan Jalur Gudang:** Pisahkan antrean *wave-picking* paket ekspres sebelum pukul 13.00 dan sesuaikan janji SLA realistis di halaman *checkout* (*First Class*: 2 hari, *Second Class*: 3 hari) untuk mengangkat OTIF aktif dari **42,7% ke >82%**.
2. **Kontrak Ulang 9 SKU Inti & Sinkronisasi Stok Web-ERP:** Terapkan *Safety Stock* 99% dan *auto-ROP* pada 8 SKU Kelas AX serta 1 SKU Kelas BX, sekaligus memperbaiki integrasi bendera `Product Status` antara ERP dan situs web.
3. **Pengendalian *Cost-to-Serve*:** Audit tarif logistik retur pada rute merugi, terapkan nilai belanja minimum (*minimum order threshold*), dan batasi plafon diskon promosi maksimal 15%.
4. **Otorisasi Otomatis Kanal `TRANSFER`:** Tahan alokasi stok gudang sebelum dana `TRANSFER` terverifikasi otomatis (*virtual account / escrow*) dan berlakukan batas waktu bayar 24 jam pada status `PENDING_PAYMENT`.
