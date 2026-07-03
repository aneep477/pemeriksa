# Panduan Setup — Sistem Moderasi Skrip Pelajar (Groq AI)

## Bahagian 1: Google Sheet + Apps Script (Backend)

1. Cipta **Google Sheet baharu** (kosong pun tak apa — sistem akan cipta sheet
   `RubricTemplates` dan `Submissions` secara automatik).
2. Salin **Sheet ID** daripada URL:
   `https://docs.google.com/spreadsheets/d/SHEET_ID_DI_SINI/edit`
3. Buka **Extensions > Apps Script** dari Google Sheet tersebut.
4. Padam kod default, salin-tampal seluruh kandungan `Code.gs` yang disediakan.
5. Tukar baris ini di bahagian atas kod:
   ```
   const SHEET_ID = 'MASUKKAN_GOOGLE_SHEET_ID_ANDA_DI_SINI';
   ```
   kepada Sheet ID sebenar anda.

### Tetapkan token Groq API (JANGAN letak dalam kod)
6. Di Apps Script editor: **Project Settings** (ikon gear) > **Script Properties**
   > **Add script property**
   - Property: `GROQ_API_KEY`
   - Value: token Groq anda (dapatkan di console.groq.com)

### Aktifkan Drive API (untuk OCR PDF)
7. Di Apps Script editor: klik **Services** (ikon +) > cari **Drive API**
   > pilih versi **v2** > Add.

### Deploy sebagai Web App
8. Klik **Deploy > New deployment**
   - Type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
9. Klik **Deploy**, benarkan kebenaran (authorize) yang diminta.
10. Salin **URL Web App** yang terhasil (format: `https://script.google.com/macros/s/XXXX/exec`)

> ⚠️ **PENTING**: Setiap kali anda ubah `Code.gs` selepas ini, anda WAJIB pergi ke
> **Deploy > Manage deployments > ✏️ Edit > Version: New version > Deploy**
> supaya perubahan hidup di URL yang sama. Sekadar "Save" sahaja TIDAK mencukupi.

## Bahagian 2: Frontend (GitHub Pages)

1. Buka fail `index.html`, cari baris:
   ```js
   const WEB_APP_URL = 'MASUKKAN_URL_WEB_APP_APPS_SCRIPT_ANDA_DI_SINI';
   ```
   Tukar kepada URL Web App daripada Langkah 10 di atas.
2. Cipta repo baharu di GitHub (contoh: `sistem-moderasi-skrip`), upload `index.html`.
3. Pergi ke **Settings > Pages** > Source: `main` branch, folder `/ (root)` > Save.
4. Selepas beberapa minit, web app anda boleh diakses di:
   `https://<username-github>.github.io/<nama-repo>/`

## Alur Penggunaan

1. **Langkah 1 — Rubrik**: Cipta rubrik baharu (kod kriteria, deskriptor, markah
   penuh setiap kriteria) atau pilih rubrik sedia ada yang telah disimpan.
2. **Langkah 2 — Upload**: Upload berpuluh PDF skrip pelajar sekaligus. Nama fail
   automatik jadi nama pelajar (boleh disunting).
3. **Langkah 3 — Analisis**: Sistem extract teks setiap PDF (guna Drive OCR),
   hantar ke Groq AI untuk disemak mengikut rubrik, simpan hasil ke Google Sheets.
4. **Langkah 4 — Keputusan**: Papar jadual markah semua pelajar, klik "lihat
   butiran" untuk justifikasi & bukti setiap kriteria, eksport ke CSV.

## Nota Keselamatan
- Token Groq API **hanya** disimpan dalam `PropertiesService` di backend —
  tidak pernah terdedah di frontend/browser.
- Semua markah AI adalah **cadangan** — sistem sentiasa menyatakan ini dan
  memerlukan pertimbangan akhir pensyarah.

## Had & Isu Lazim
- **PDF hasil imbasan kurang jelas** boleh menyebabkan OCR tidak tepat — semak
  teks yang diekstrak jika markah nampak pelik.
- **Had masa Apps Script** (6 minit setiap eksekusi) — jika batch terlalu besar
  (>30 skrip serentak), pertimbangkan proses dalam kumpulan kecil.
- Model Groq lalai: `llama-3.3-70b-versatile` — boleh ditukar di `GROQ_MODEL`
  dalam `Code.gs` jika perlu.
