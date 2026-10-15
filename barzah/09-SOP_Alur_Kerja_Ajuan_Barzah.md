# SOP Alur Kerja Ajuan Barzah (Layanan Mobil Jenazah)

**Dokumen** : `09-SOP_Alur_Kerja_Ajuan_Barzah.md` — SOP Operasional Barzah
**Versi** : 2.4 (dokumen dipindahkan ke folder barzah/)
**Tanggal** : 26 Agustus 2026
**Berlaku untuk** : LPM Dompet Dhuafa — seluruh cabang

Semua halaman Barzah dikelompokkan di folder **`lpm2/barzah/`** dan diberi nomor sesuai urutan alur. Penamaan memakai pola: `NN-listing-*` untuk halaman daftar data, `NN-listing-*-form-*` untuk halaman form, `NN-magic-link-*` untuk halaman petugas yang dibuka tanpa login, dan dokumen diakhiri nomor.

| # | Halaman | Untuk siapa |
| --- | --- | --- |
| 1 | `01-listing-ajuan.html` | Customer Care — daftar ajuan + tombol **Ajuan Baru** |
| 2 | `02-listing-ajuan-form-tambah.html` | Customer Care — form tambah ajuan |
| 3 | `03-listing-perintah-delivery.html` | Koordinator — daftar ajuan masuk, tombol Setujui / Tolak |
| 4 | `04-listing-perintah-delivery-form-tambah.html` | Koordinator — form perintah delivery + uang muka |
| 5 | `05-listing-realisasi.html` | Koordinator — daftar realisasi yang dilaporkan petugas |
| 6 | `06-listing-realisasi-form-verifikasi.html` | Koordinator — form verifikasi / persetujuan realisasi |
| 7 | `07-magic-link-petugas.html` | Petugas/driver — **tanpa login**, halaman tugas |
| 8 | `08-magic-link-petugas-laporan.html` | Petugas/driver — **tanpa login**, form laporan perjalanan |
| 9 | `09-SOP_Alur_Kerja_Ajuan_Barzah.md` | Dokumen ini |
| - | `shared/sidebar-barzah.html` | Sidebar bersama seluruh halaman Barzah |

Seluruh path di dalam dokumen ini relatif terhadap folder ``.

Aturan pemisahan: setiap halaman `NN-listing-*` **tidak** memuat form. Aksi pada tabel selalu membuka halaman `*-form-*` atau `magic-link-*` tersendiri lewat query string, misalnya `04-listing-perintah-delivery-form-tambah.html?no=LMS-2026-000026` atau `06-listing-realisasi-form-verifikasi.html?no=LMS-2026-000023&mode=verifikasi`.

---

## 1. Alur Singkat

```
[1] Ajuan Baru (Customer Care)  ->  [2] Intake Barzah (isi detail)
                                          |  Simpan & Kirim ke Koordinator
                                          v
[3] Perintah Delivery (Koordinator): Setujui -> pilih petugas, waktu,
    catatan, nilai uang muka, tanggal laporan -> Kirim Perintah
                                          |  magic link terkirim (WA/email)
                                          v  status = MENUNGGU
[4] Halaman Petugas (tanpa login): "Terima Delivery" -> status = DALAM PERJALANAN
                                          |  "Selesai Perjalanan"
                                          v
[5] Laporan Perjalanan (isi rincian biaya) -> Kirim Laporan -> status = SELESAI
```

Nominal uang muka keluar saat perintah dikirim, tetapi **nilai manfaat di Benefis berasal dari rincian biaya pada laporan perjalanan, bukan dari uang muka.**

---

## 2. Langkah 1 & 2 — Ajuan Baru dari Customer Care

1. Buka `01-listing-ajuan.html`, klik tombol **+ Ajuan Baru**.
2. Pergi ke `02-listing-ajuan-form-tambah.html` untuk mengisi detail.
3. Klik **Simpan & Kirim ke Koordinator**. Status ajuan menjadi **Ajuan Masuk**.

### Field intake

| Field | Wajib | Keterangan |
| --- | --- | --- |
| Nama Almarhum / Almarhumah | ya | Nama sesuai KTP |
| Nama Keluarga / Pelapor | ya | Yang mengajukan permintaan |
| No. HP Pelapor | ya | Nomor WhatsApp utama |
| Alamat Pelapor | ya | Alamat lengkap + RT/RW |
| Lokasi Jenazah / Saat Ini | ya | Titik jemput |
| Lokasi Tujuan | ya | RS, ruang jenazah, atau lokasi pemakaman |
| Jenis Kelamin / Usia / Penyebab Kematian | tidak | Pendukung |
| Hubungan dengan Jenazah | tidak | Istri / Anak / Suami / Orang Tua |
| Waktu Dibutuhkan | tidak | Kapan jenazah harus sampai |
| Catatan Khusus | tidak | Kondisi jenazah, akses jalan, permintaan khusus |
| Lampiran | tidak | Foto / surat keterangan (maks 10 MB) |

Sebelum simpan, **cek duplikasi** NIK jenazah / No. KK pelapor ke data-benefisi di Benefis agar tidak ada permintaan ganda.

---

## 3. Langkah 3 — Perintah Delivery (Koordinator)

Buka `03-listing-perintah-delivery.html`. Halaman ini adalah **daftar ajuan yang masuk**, bukan satu dokumen.

### 3.1 Status yang terlihat

| Status | Arti | Aksi tersedia |
| --- | --- | --- |
| Ajuan Masuk | baru dari Customer Care, belum diputus | Setujui / Tolak |
| Menunggu | perintah sudah dikirim ke petugas | Lihat Perintah |
| Dalam Perjalanan | petugas sudah menerima tugas | Lihat |
| Selesai | laporan perjalanan sudah dikirim | Lihat Laporan |
| Ditolak | tidak dilanjutkan | Lihat alasan |

### 3.2 Klik Setujui

Tombol **Setujui** membuka halaman baru `04-listing-perintah-delivery-form-tambah.html` (form tidak digabung di halaman daftar). Nomor ajuan diteruskan lewat query string, misalnya `04-listing-perintah-delivery-form-tambah.html?no=LMS-2026-000026`. Form terdiri atas dua bagian:

**A. Delivery**

| Field | Wajib | Keterangan |
| --- | --- | --- |
| Petugas Delivery | ya | Dari master petugas |
| Tanggal & Waktu Berangkat | ya | Waktu mulai |
| Estimasi Tiba | tidak | Perkiraan sampai |
| Catatan untuk Petugas | tidak | Instruksi khusus, tampil di halaman petugas |

**B. Uang Muka Perjalanan**

| Field | Wajib | Keterangan |
| --- | --- | --- |
| Nilai Uang Muka (Rp) | ya | Ongkos perjalanan, auto-format ribuan. Tombol "Isi tarif standar" mengisi saran tarif |
| Metode Serah Terima | ya | Tunai / Transfer |
| Tanggal Laporan Uang Muka | ya | Batas petugas mengirim laporan |

### 3.3 Kirim magic link

1. Klik **Generate Link**.
2. Pilih kanal: **WhatsApp**, **Email**, atau **Copy Link**. Tombol **Buka Halaman Petugas** untuk melihat tampilan yang diterima petugas.
3. Klik **Kirim Perintah Delivery**.

Setelah dikirim: badge status baris berubah menjadi **Menunggu**, KPI "Ajuan Masuk" berkurang, KPI "Menunggu" bertambah.

### 3.4 Menolak ajuan

Tombol **Tolak** meminta alasan (data tidak lengkap / dokumen tidak valid / melewati batas waktu / lainnya). Baris berubah menjadi **Ditolak** dan alasannya tampil.

---

## 4. Langkah 4 — Halaman Petugas (`07-magic-link-petugas.html`)

Petugas membuka magic link **tanpa login**. Isi halaman: data jenazah, pelapor + nomor HP, rute jemput ke tujuan, waktu keberangkatan, nominal uang muka yang diterima, dan catatan Koordinator.

Stepper: Diterima → Perjalanan → Laporan.

| Tombol | Aksi |
| --- | --- |
| **Terima Delivery** | Mengambil tugas. Status berubah dari Menunggu menjadi **Dalam Perjalanan**. Tombol berganti menjadi "Selesai Perjalanan" |
| **Selesai Perjalanan** | Menandai perjalanan selesai dan memunculkan tombol **Isi Laporan Perjalanan** yang menuju `08-magic-link-petugas-laporan.html` |

Magic link bersifat sekali pakai dan punya masa berlaku.

---

## 5. Langkah 5 — Laporan Perjalanan (`08-magic-link-petugas-laporan.html`)

Halaman ini diisi petugas, juga tanpa login.

| Bagian | Isi |
| --- | --- |
| 1. Info Perjalanan | Waktu tiba (wajib), kendala, catatan perjalanan |
| 2. Rincian Biaya Terpakai | Baris dinamis: kategori (BBM/Transportasi, Tol & Parkir, Ojol/Angkut Jenazah, Layanan Jenazah, **Perlengkapan Jenazah (Kain Kaffan, Kapas, dst)**, Adhkarial/PAB, Honor Petugas & Tim, Lain-lain) + nominal + keterangan. Total dihitung otomatis |
| 3. Uang Muka | Diterima / Terpakai / Sisa. Bila ada sisa, isi tanggal dan metode pengembalian. Bila realisasi melebihi uang muka, wajib isi alasan |
| 4. Bukti | Nota atau kuitansi pengeluaran, bukti serah terima jenazah |

Klik **Kirim Laporan** untuk menutup tugas. Status ajuan menjadi **Selesai**, dan layar konfirmasi menampilkan ringkasan: uang muka, realisasi, sisa dikembalikan, dan nilai transaksi yang dicatat.

Aturan main:
- **Nilai transaksi di Benefis = total realisasi terpakai.**
- Sisa uang muka wajib dikembalikan ke Koordinator.
- Realisasi melebihi uang muka wajib disertai alasan, transaksi dicatat sebelum Koordinator memverifikasi.

---

## 6. Rekap & Laporan Uang

`05-listing-realisasi.html` menampilkan rekap per perjalanan: uang muka, realisasi, sisa atau selisih, batas laporan, status, dan `distribution_uuid`. KPI: total uang muka, total realisasi, sisa belum dikembalikan, selisih lebih.

`lpm-export-laporan.html`: pilih **Layanan = Barzah (Mobil Jenazah)**, Jenis Laporan *Realisasi Uang Muka (Barzah)* atau *Rekap Delivery Barzah*.

---

## 7. Tabel Status

| # | Status | Dipicu oleh |
| --- | --- | --- |
| 1 | AJUAN_MASUK | Customer Care menyimpan ajuan |
| 2 | MENUNGGU | Koordinator mengirim perintah delivery |
| 3 | DALAM_PERJALANAN | Petugas klik Terima Delivery |
| 4 | SELESAI | Petugas mengirim laporan perjalanan |
| 5 | DITOLAK | Koordinator menolak ajuan |

Setiap perpindahan status menulis 1 baris audit (`status_log`).

---

## 8. Peta Data

| Entitas | Isi |
| --- | --- |
| `lpm_request` | Ajuan Barzah |
| `approval` | Keputusan Koordinator |
| `lpm_delivery` | Petugas, jadwal, uang muka, tanggal laporan, status |
| `lpm_delivery_cost` | Rincian biaya terpakai (banyak baris per delivery) |
| `status_log` | Riwayat perubahan status |
| `distribution` | Transaksi manfaat, nilai = total realisasi, program `LPM - Barzah` |

---

## 9. Edge Case

| Situasi | Penanganan |
| --- | --- |
| Jenazah sudah dikuburkan saat ajuan masuk | Ditolak dengan alasan melewati batas waktu |
| Petugas tidak menerima tugas sampai lewat hari H | Koordinator membatalkan assignment dan menugaskan petugas lain |
| Petugas membatalkan di tengah jalan | Status kembali Menunggu, Koordinator menugaskan ulang |
| Realisasi melebihi uang muka | Wajib menyertakan alasan; menunggu verifikasi Koordinator |
| Petugas tidak mengembalikan sisa uang muka | Dicatat di rekap "Sisa Belum Dikembalikan", Koordinator menindaklanjuti |
| Pengajuan ulang keluarga yang sama | Diizinkan; cek duplikasi hanya untuk orang yang sama |
| Jenazah di luar wilayah kerja LPM | Ditolak atau diteruskan ke LPM setempat |

---

## 10. Referensi

`PRD_v2_Pencatatan_Approval_LPM (1).md` §3.3, §6.7, §6.7b, §9.3 · `BRD_Platform_Benefis_LPM_2026-2027_v2.0.docx.md` §10.3
