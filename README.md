# Jawa Ginger · Dashboard untuk GitHub Pages

Dashboard 11 panel dengan tampilan glass smoky dan aksen oranye, berdasarkan kode proyek Jawa Ginger sebelumnya. Tidak membutuhkan Terminal, npm, backend, atau Google Apps Script. Buka **PANDUAN.html** untuk tutorial yang bisa dibaca seperti halaman web.

## Yang disiapkan

- `index.html`: aplikasi lengkap, termasuk CSS, grafik, tabel, dan kalkulasi.
- `config.js`: pengaturan Google OAuth Client ID dan spreadsheet.
- `PANDUAN.html`: tutorial dari membuat akun sampai dashboard membaca data.
- `.nojekyll`: file kosong untuk hosting statis; jika tidak terlihat saat upload, aplikasi tetap tidak memerlukan Jekyll.

## Mulai dari nol

### 1. Buat akun GitHub

1. Buka https://github.com dan pilih **Sign up**.
2. Masukkan email, password, dan username. Selesaikan verifikasi email.
3. Catat username. Contoh panduan ini menggunakan `gelemo`; ganti dengan username kamu sendiri.

GitHub menyimpan kode dalam **repository**, seperti folder proyek online. GitHub Pages menayangkan kode HTML tersebut sebagai website. Untuk GitHub Free, gunakan repository **Public**. Kode dan website bisa dilihat publik; data spreadsheet tetap memerlukan izin Google karena tidak disertakan dalam kode.

### 2. Buat repository

1. Setelah login, buka https://github.com/new.
2. Repository name: `jawa-ginger-dashboard`.
3. Pilih **Public**.
4. Aktifkan **Add README** jika pilihan tersedia.
5. Klik **Create repository**.

Nama repository tidak perlu `username.github.io`; paket ini menggunakan project site dengan alamat `https://USERNAME.github.io/jawa-ginger-dashboard/`.

### 3. Upload file aplikasi

1. Download ZIP yang disediakan dan ekstrak di komputer. Jangan upload ZIP utuh.
2. Masuk ke repository, lalu pilih **Add file → Upload files**.
3. Seret **isi** folder `jawa-ginger-github`, bukan folder induknya. Upload `index.html`, `config.js`, `README.md`, dan `PANDUAN.html`.
4. Isi pesan commit, misalnya `Upload dashboard pertama`, lalu klik **Commit changes**.
5. Pastikan `index.html` dan `config.js` terlihat langsung di halaman utama repository. Jangan berada satu tingkat di dalam folder lain.

**Commit** berarti menyimpan versi perubahan. Kamu bisa mengedit kembali file kapan saja.

### 4. Nyalakan GitHub Pages

1. Di repository, buka **Settings → Pages**.
2. Pada **Build and deployment**, pilih Source: **Deploy from a branch**.
3. Branch: **main**. Folder: **/(root)**. Klik **Save**.
4. Tunggu proses deployment; publikasi dapat membutuhkan hingga sekitar 10 menit.
5. Buka kembali Settings → Pages, lalu klik **Visit site**.
6. Contoh URL: `https://gelemo.github.io/jawa-ginger-dashboard/`.

Pada tahap ini tampilan dashboard muncul, tetapi angka masih kosong sampai login Google selesai dikonfigurasi. Ini keadaan yang diharapkan. File tidak berisi salinan data bisnis atau angka demo.

### 5. Siapkan project Google Cloud

1. Buka https://console.cloud.google.com menggunakan akun Google yang bisa membaca spreadsheet Jawa Ginger.
2. Klik pemilih project di bagian atas, lalu **New project**.
3. Nama: `Jawa Ginger Dashboard`. Klik **Create**, lalu pilih project tersebut.
4. Buka **APIs & Services → Library**.
5. Cari **Google Sheets API**, buka, lalu klik **Enable**.
6. Kembali ke Library. Cari **Google Drive API** dan klik **Enable**.

Sheets API membaca tabel. Drive API membaca waktu file terakhir diubah untuk **Last Update Data**. Dashboard hanya membaca, tidak menulis ke spreadsheet.

### 6. Atur izin login Google

Nama menu Google dapat berbeda menurut bahasa dan akun. Antarmuka baru memakai **Google Auth Platform**; antarmuka lama memakai **APIs & Services → OAuth consent screen**.

1. Buka **Google Auth Platform**. Jika diminta, klik **Get started**.
2. Di **Branding**, isi App name: `Jawa Ginger Dashboard`, User support email, dan Developer contact email dengan email kamu.
3. Untuk akun Gmail pribadi, pilih Audience **External**. Pilihan **Internal** hanya tersedia pada organisasi Google Workspace tertentu.
4. Biarkan status aplikasi **Testing** untuk penggunaan pribadi/tim kecil.
5. Pada **Audience → Test users**, klik **Add users** dan tambahkan email Google kamu. Tambahkan email rekan yang akan menggunakan dashboard bila diperlukan.
6. Pada **Data Access → Add or remove scopes**, tambahkan dua scope ini (bisa ditempel di kolom scope manual):

```text
https://www.googleapis.com/auth/spreadsheets.readonly
https://www.googleapis.com/auth/drive.metadata.readonly
```

7. Simpan pengaturan.

Izin Sheets berlaku membaca spreadsheet yang dapat diakses akun; izin Drive berlaku membaca metadata file akun. Kode aplikasi ini hanya meminta ID spreadsheet yang dikonfigurasi. Client ID sendiri tidak membatasi izin OAuth menjadi satu file. Jangan pilih izin menulis atau akses penuh Drive.

Pengguna harus sekaligus menjadi test user OAuth **dan** mempunyai akses Viewer atau lebih tinggi ke spreadsheet. Menambah test user tidak memberi akses ke isi spreadsheet. Pertahankan akses spreadsheet **Restricted**; tidak perlu mengubah menjadi Anyone with the link.

Mode Testing cocok untuk memulai. Untuk penggunaan luas, tinjau persyaratan publikasi/verifikasi Google. Jangan langsung mengubah ke Production dengan asumsi semua akun otomatis diizinkan.

### 7. Buat OAuth Client ID

1. Buka **Google Auth Platform → Clients → Create client**. Pada tampilan lama: **APIs & Services → Credentials → Create credentials → OAuth client ID**.
2. Application type: **Web application**.
3. Nama: `Jawa Ginger GitHub Pages`.
4. Pada **Authorized JavaScript origins**, tambahkan origin website:

```text
https://USERNAME.github.io
```

5. Ganti `USERNAME` dengan username GitHub kamu. Contoh: `https://gelemo.github.io`.
6. **Jangan** masukkan `/jawa-ginger-dashboard/` pada origin. Jangan masukkan URL `github.com` repository. Jangan pakai tanda slash penutup.
7. **Authorized redirect URIs** boleh kosong: aplikasi ini menggunakan popup Google Identity Services.
8. Klik **Create**. Salin **Client ID**, yang biasanya diakhiri `.apps.googleusercontent.com`.

Client ID memang digunakan di browser dan boleh ada dalam repository publik. **Client Secret tidak dipakai**; jangan upload Client Secret, file kredensial JSON, token, CSV, atau Excel sumber.

### 8. Isi config.js

1. Kembali ke repository GitHub → klik `config.js` → klik ikon pensil **Edit this file**.
2. Ganti nilai placeholder dengan Client ID kamu:

```javascript
window.JG_CONFIG = {
  googleClientId: 'CLIENT_ID_KAMU.apps.googleusercontent.com',
  spreadsheetId: '1xoZnBVFTLJNkzrCxYXGyUwUKDZv26m0OMZNUbiBNpJY'
};
```

3. Pertahankan tanda kutip dan koma. Client ID lengkap cukup ditempel sekali; jangan menambahkan akhiran dua kali.
4. Klik **Commit changes**, lalu simpan ke branch main.
5. Tunggu deployment Pages berikutnya selesai, kemudian buka website dan refresh. Jika konfigurasi lama masih muncul, lakukan hard refresh (Mac: Cmd+Shift+R; Windows: Ctrl+Shift+R).

### 9. Hubungkan Google dan periksa data

1. Klik **Hubungkan Google** di bagian atas dashboard.
2. Pilih akun yang ditambahkan sebagai test user dan punya akses ke spreadsheet.
3. Tinjau nama aplikasi dan izin baca, lalu setujui. Jika muncul peringatan aplikasi belum diverifikasi pada project Testing milikmu, lanjutkan hanya setelah memastikan project, Client ID, dan izin sesuai pengaturan yang kamu buat. Jika Google memblokir atau admin melarang, selesaikan pengaturan/izin dahulu.
4. Tunggu pembacaan tab selesai. Ringkasan bawah menampilkan jumlah sumber terbaca, idealnya **10/10**.
5. Periksa Ringkasan, Marketplace, Stok, dan Pembayaran. Bandingkan angka dengan spreadsheet untuk periode yang sama.
6. Klik transaksi untuk melihat detail baris. Gunakan filter periode, area, status, PIC, dan pencarian sesuai panel.

**11 panel:** Ringkasan, Sales pipeline, Mitra & partner, Penjualan, Produk, Marketplace, Stok & warehouse, Order & fulfillment, Pembayaran, Logistik, Sample.

### 10. Pemakaian sehari-hari

- Ubah data di spreadsheet seperti biasa, lalu klik **Refresh data** di dashboard.
- Dashboard tidak otomatis membaca saat spreadsheet berubah. Data dibaca setelah login dan saat Refresh ditekan.
- **Last Update Data** adalah waktu modifikasi file menurut Google Drive, bukan waktu edit setiap baris. Kalkulasi otomatis/formula dapat memiliki perilaku berbeda.
- **Last refresh** adalah waktu siklus pembacaan selesai. Jika sebagian tab gagal, panel memperlihatkan peringatan dan data lama tab itu, bila tersedia. Siklus pembacaan bukan snapshot transaksi yang atomik.
- Token login berumur pendek. Jika sesi habis, klik **Ganti akun** untuk menghubungkan lagi. Setelah reload/tutup halaman, login lagi; token dan data tidak disimpan ke localStorage.
- **Keluar** menghapus token dan data dari halaman. Untuk mencabut izin aplikasi Google secara permanen, buka https://myaccount.google.com/connections.
- Jika teman akan memakai, tambahkan sebagai test user dan bagikan spreadsheet kepada emailnya. Kirim URL GitHub Pages, bukan URL repository.

## Jika ada masalah

| Gejala | Yang diperiksa |
|---|---|
| Website 404 | Pages sudah aktif, main + root dipilih, index.html berada di root, deployment selesai. |
| Client ID belum diisi | Edit config.js, commit perubahan, tunggu Pages, hard refresh. |
| origin_mismatch | Authorized JavaScript origins harus persis https://USERNAME.github.io tanpa path. |
| redirect_uri_mismatch | Pastikan client Web application dan memakai konfigurasi paket ini; jangan mengubah alur menjadi redirect. |
| access_denied / akun diblokir | Tambahkan email ke Test users; periksa policy admin Workspace dan status OAuth. |
| Popup tidak terbuka | Izinkan popup untuk domain GitHub Pages; periksa pemblokir skrip dan internet. |
| API disabled / accessNotConfigured | Aktifkan Sheets API dan Drive API pada project yang sama dengan OAuth Client ID. |
| Akses spreadsheet ditolak | Gunakan akun yang punya akses; periksa spreadsheetId di config.js. |
| Last Update Data belum tersedia | Drive API aktif dan izin drive.metadata.readonly disetujui; hubungkan ulang Google jika baru mengubah scope. |
| Sumber kurang dari 10/10 | Baca error panel; pastikan semua nama tab sama persis dengan sumber. |
| Angka tampak berbeda | Periksa filter, definisi KPI, tanggal tidak valid, status pembayaran, dan baris formula error. |
| Google 429 | Tunggu beberapa saat lalu Refresh; jangan klik berulang cepat. |

## Batas perhitungan dan validasi

Omzet menggabungkan setoran mitra sebelum potongan dan gross marketplace. Nilai order tidak ditambahkan ke omzet. Outstanding hanya memakai status invoice/pending yang dikenal; status kosong tidak diasumsikan belum dibayar. Saldo stok berasal dari mutasi masuk minus keluar, bukan stock opname. Tanggal tak valid ikut total semua periode tetapi tidak grafik bulanan. Rincian definisi tersedia lewat tombol ⓘ.

Pemetaan kolom mengikuti spreadsheet proyek sebelumnya. Jika urutan kolom berubah, kode pemetaan perlu disesuaikan. Paket tidak memuat snapshot data bisnis. Pengujian lokal memeriksa perhitungan 11 panel, penanganan sumber gagal, tanggal/filter, serta alur koneksi dengan respons tiruan. Login sungguhan dan GitHub Pages baru dapat diverifikasi setelah Client ID dan repository milikmu disiapkan.

## Referensi resmi

- GitHub Pages: https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site
- Sumber deployment: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
- Google OAuth Client ID: https://developers.google.com/identity/oauth2/web/guides/get-google-api-clientid
- Google token model: https://developers.google.com/identity/oauth2/web/guides/use-token-model
- Google Sheets API: https://developers.google.com/workspace/sheets/api/reference/rest/v4/spreadsheets.values/get
- Google Drive metadata: https://developers.google.com/workspace/drive/api/reference/rest/v3/files/get
