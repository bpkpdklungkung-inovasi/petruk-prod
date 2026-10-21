# PETRUK — GitHub Pages + Google Sheets

Versi ini disusun khusus untuk **GitHub Pages**. `index.html` tidak menggunakan `google.script.run`, karena GitHub Pages bukan bagian dari Google Apps Script.

## Arsitektur

```text
GitHub Pages
  index.html + JavaScript
        │
        │ JSONP HTTPS
        ▼
Google Apps Script Web App (/exec)
        │
        ▼
Google Spreadsheet
  ├── Data Reklame
  └── Users
```

## 1. Siapkan Google Spreadsheet

Buat/siapkan sheet:
- `Data Reklame`
- `Users`

Kolom mengikuti struktur yang digunakan versi PETRUK sebelumnya.

## 2. Pasang Code.gs

1. Buka Google Spreadsheet.
2. **Extensions → Apps Script**.
3. Hapus Code.gs lama dan masukkan `Code.gs` dari paket ini.
4. Isi `CFG.SPREADSHEET_ID` jika project Apps Script dibuat standalone. Jika script dibuat dari Spreadsheet, boleh dikosongkan.
5. Jalankan `testSpreadsheetConnection()` sekali dan berikan izin.

## 3. Deploy Google Apps Script

**Deploy → New deployment → Web app**

- Execute as: **Me**
- Who has access: pilih akses yang sesuai, misalnya **Anyone** untuk aplikasi publik.

Salin URL yang berakhiran `/exec`, misalnya:

`https://script.google.com/macros/s/AKfycbxxxxxxxx/exec`

## 4. Masukkan URL ke index.html

Buka `index.html`, cari:

```javascript
const GAS_WEB_APP_URL = 'https://script.google.com/macros/s/DEPLOYMENT_ID/exec';
```

Ganti dengan URL `/exec` Anda.

Alternatif tanpa mengedit file, URL dapat diberikan melalui parameter GitHub Pages:

`https://USERNAME.github.io/REPOSITORY/?gas=https%3A%2F%2Fscript.google.com%2Fmacros%2Fs%2FDEPLOYMENT_ID%2Fexec`

## 5. Upload ke GitHub

Letakkan minimal:
- `index.html`
- `petruk_web.png`

`Code.gs` **tidak perlu** dipublikasikan ke GitHub jika Anda ingin menjaga source backend tetap privat. Simpan Code.gs di project Google Apps Script.

Aktifkan **Settings → Pages → Deploy from branch**.

## Catatan keamanan

Frontend GitHub Pages memanggil Apps Script menggunakan JSONP agar dapat bekerja lintas domain. Karena JSONP menggunakan GET, parameter login dikirim melalui HTTPS sebagai query string; untuk aplikasi produksi dengan kebutuhan keamanan tinggi, sebaiknya gunakan mekanisme autentikasi/API gateway yang mendukung CORS/POST. Jangan memasukkan password atau secret Google ke `index.html`.

Peta publik menggunakan endpoint `getPublicMapData()` yang memang dirancang hanya mengirim data peta. Operasi admin/operator tetap membutuhkan token sesi dari Apps Script.
