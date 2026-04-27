# Jurnal Quran

PWA untuk tracking progress baca Al-Quran harian. Semua data disimpan lokal di browser (localStorage), tidak ada backend.

## Struktur Folder

```
.
├── index.html          # Entry point — hanya HTML shell + link ke style.css & app.js
├── app.jsx             # Source utama — semua komponen React + logika app (EDIT DI SINI)
├── app.js              # Hasil compile dari app.jsx — jangan diedit manual
├── style.css           # Hasil build Tailwind — jangan diedit manual
├── tailwind.config.js  # Konfigurasi Tailwind (darkMode: 'class', content scan)
├── sw.js               # Service Worker — cache strategy untuk offline support
├── manifest.json       # PWA manifest (nama, ikon, theme color, dll)
├── package.json        # Dev dependencies + build scripts
├── icon.png            # Ikon app (apple-touch-icon)
├── icon-192-2.png      # Ikon PWA 192x192
└── icon-512-2.png      # Ikon PWA 512x512
```

## Workflow Edit Kode

**Selalu edit `app.jsx`, bukan `app.js`.**

Setelah selesai edit, jalankan build:

```bash
# Build sekali (JS + CSS)
npm run build

# Atau build terpisah
npm run build:js    # compile app.jsx → app.js
npm run build:css   # build tailwind → style.css (minified, tanpa file input)
```

Untuk development dengan auto-rebuild saat file berubah, buka dua terminal:

```bash
# Terminal 1
npm run watch:js

# Terminal 2
npm run watch:css
```

> Setelah `watch:css`, Tailwind tidak otomatis minify. Jalankan `npm run build:css` sebelum deploy untuk ukuran file optimal.

## Arsitektur

App ini single-file React tanpa bundler (Vite/Webpack). Semua komponen ada di `app.jsx`:

| Komponen | Fungsi |
|---|---|
| `App` | Root component, semua state global ada di sini |
| `DzikirView` | Counter dzikir pagi/petang dengan feedback suara + getar |
| `MonthlyCalendar` | Kalender baca + toggle haid per hari |
| `KhatamTarget` | Hitung target khatam berdasarkan tanggal |
| `JuzTrack` | Visual progress bar per juz |
| `LastReadBanner` | Banner surah terakhir dibaca |
| `CompactInput` | Input ayat inline per surah |
| `Icon` | SVG icon component (inline, tidak ada dependency) |
| `KhatamModal` | Modal celebrasi saat khatam |

## Data & State

Semua data persist di `localStorage` dengan key prefix `q-`:

| Key | Isi |
|---|---|
| `q-progress` | Object `{ [surahNumber]: ayatTerakhir }` |
| `q-last` | Nomor surah terakhir dibaca |
| `q-streak` | Jumlah hari streak aktif |
| `q-date` | Tanggal terakhir aktif (format `en-CA`: YYYY-MM-DD) |
| `q-khatam` | Jumlah khatam |
| `q-haid-log` | Object `{ [dateKey]: true }` untuk hari haid |
| `q-daily-log` | Object `{ [dateKey]: jumlahAyat }` log harian |
| `q-dark` | Boolean dark mode |
| `q-tjuz` | Target juz hari ini |
| `q-tdate` | Tanggal target harian |
| `q-dstart` | Total ayat saat target harian dimulai |
| `q-page-mode` | `'ayat'` atau `'halaman'` |
| `q-target-mode` | `'juz'` atau `'halaman'` |
| `q-page-target` | Target halaman per hari |
| `q-khatam-target` | Tanggal target khatam |
| `q-dzikir-active` | Sesi dzikir terakhir dibuka (`pagi` atau `petang`) |
| `q-dzikir-counts` | Counter dzikir harian per sesi |

## Service Worker

Cache strategy di `sw.js`:
- **HTML/navigasi** → network first, fallback ke cache
- **CDN external** (React, Google Fonts) → cache first, fallback network
- **Asset lokal** (`app.js`, `style.css`, dll) → cache first

Setiap kali deploy versi baru, **update `CACHE_NAME`** di `sw.js` (misal `v8` → `v9`) supaya SW lama di-invalidate dan user dapat versi terbaru.

## Performance

Sebelum optimasi vs sesudah:

| | Sebelum | Sesudah |
|---|---|---|
| `@babel/standalone` | ~1.5MB | dihapus |
| Tailwind CDN | ~350KB | 25KB (build) |
| `app.js` | — | 121KB |
| Total JS baru | ~2MB+ | ~271KB |

React + ReactDOM (~150KB) masih dari CDN unpkg dan di-cache oleh service worker setelah pertama kali load.
