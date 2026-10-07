# Analisis Duta GeoSpasi — geospasi.dutamik.id

Disalin: 7 Oktober 2026 dari `https://geospasi.dutamik.id` ke `D:\a` (total ±7,2 MB).
Judul aplikasi: **Duta GeoSpasi : Sistem Digitalisasi Geospasial Indonesia** (SHP Builder).

## 1. Struktur File

| File | Fungsi |
|---|---|
| `index.html` | Aplikasi utama (single-file, ±443 KB: HTML + CSS + JS inline, ±230 fungsi) |
| `SHP-Builder.html` | Identik dengan `index.html` (hash sama) — alias URL `/SHP-Builder` |
| `bantuan.html`, `panduan-fitur.html` | Halaman bantuan & panduan |
| `hubungi-kami.html`, `jaminan-privasi.html`, `syarat-ketentuan.html`, `penafian-layanan.html` | Halaman legal / kontak |
| `404.html` | Halaman error |
| `assets/img/` | `favicon.ico`, `logo.png` (742 KB – bisa dikompres ke WebP), `qris-duta.jpg` |
| `assets/js/` | `jszip.min.js`, `html2pdf.bundle.min.js` (fallback lokal dari CDN) |
| `data/wilayah_indonesia.json` | Data wilayah Provinsi → Kabupaten → Kecamatan → Desa (4,7 MB) |

## 2. Teknologi

- **Peta:** Leaflet 1.9.4 + Leaflet.draw 1.0.4 (unpkg CDN)
- **Basemap:** OpenStreetMap, CARTO (Light / Voyager), Esri World Imagery, Topo, Boundaries, Transportation
- **Layer tematik:** WMS ATR/BPN (`atlas.atrbpn.go.id/geoserver/wms`), Kawasan Hutan KLHK (`geoportal.planologi.kehutanan.go.id`), link ke Bhumi ATR/BPN
- **Geocoding:** Nominatim OSM (search & reverse)
- **Ekspor:** JSZip (Shapefile ZIP di sisi klien), html2pdf.js (PDF), GeoJSON, KML, XLS, CSV, TXT, SCR (AutoCAD)
- **Auth & database:** Supabase JS v2 (tabel `members`, `payments`) + Google Identity Services (login Google)
- **Pembayaran:** QRIS statis + kode unik 3 digit, konfirmasi via WhatsApp admin
- **Font:** Plus Jakarta Sans & JetBrains Mono (Google Fonts)
- **Lain-lain:** TinyURL API (pemendek link share), Schema.org JSON-LD (SEO)

## 3. Fitur Utama

1. **Pencarian lokasi** — cari alamat, koordinat (Desimal / DMS / UTM), dropdown wilayah bertingkat, GPS pengguna, link share lokasi.
2. **Digitasi poligon bidang tanah** — gambar, undo/redo, tambah/hapus patok (vertex), snap ke batas persil, presisi batas, rotasi, perbesar/perkecil, skala otomatis ke luas BPN.
3. **Pengukuran** — luas, keliling, label jarak tiap sisi, koordinat UTM (zona otomatis).
4. **Gabung bidang (merge)** — klik beberapa persil lalu gabungkan (klien atau server).
5. **Layer persil & ceking** — overlay persil BPN, kawasan hutan, opacity bisa diatur.
6. **POI kustom** — tambah titik penanda dengan ikon unggahan sendiri.
7. **Atribut kustom GIS** — tambah kolom atribut untuk Shapefile.
8. **Impor** — GeoJSON / KML / ZIP Shapefile.
9. **Ekspor** — Shapefile .ZIP resmi, GeoJSON, KML, Excel, CSV, TXT (P,E,N,Z / X,Y), Script CAD (.SCR), salin koordinat.
10. **Cetak peta** — format A4/A3 dengan kop penerbit (logo), inset peta, legenda, skala batang, grid/graticule, PDF langsung.
11. **Member FREE / PRO** — login Google lewat Supabase, upgrade PRO via QRIS, cek status pembayaran otomatis.
12. **Konfigurasi** — URL Gateway BPN, Google Client ID, Supabase key (disimpan di `localStorage`).
13. **Security shield** — `initSecurityShield` (proteksi akses / halaman 403).
14. **UI** — sidebar yang bisa di-resize, mode mobile, tab, toast notifikasi.

## 4. ⚠️ Bagian yang TIDAK Ikut Tersalin (Backend)

Sebagian fitur memanggil **server Gateway/Proxy BPN** (default `http://localhost:8085`, bisa diganti lewat menu Konfigurasi Gateway). Kode server ini **tidak publik**, jadi tidak ikut tersalin. Endpoint-nya:

```
/api/status            /api/tile/              /api/persil-info
/api/layer-ceking/     /api/batas-kelurahan    /api/merge-parcels
/api/export-shp        /api/reverse-geocode    /api/clear-cache
/api/wilayah/{all,provinsi,kabupaten,kecamatan,desa,search}
```

Tanpa gateway itu, fitur persil BPN, merge di sisi server, dan export SHP di sisi server tidak berjalan. Fitur yang bisa jalan di sisi klien saja (digitasi, ukur, ekspor GeoJSON/KML/SHP-klien, cetak) tetap berfungsi.
Kalau source code gateway (misalnya `server.js` / Python di port 8085) ada di komputer lain, salin juga supaya proyek lengkap.

## 5. Cara Menjalankan Lokal

```powershell
cd D:\a
python -m http.server 8099
# buka http://localhost:8099/index.html
```

## 6. Catatan untuk Deploy ke geospasi.dutamik.id

- Data `members` dan `payments` di Supabase dibuka dengan anon key dari browser, jadi **Row Level Security (RLS) wajib aktif**.
- `logo.png` (742 KB) sebaiknya dikonversi ke WebP (<80 KB) agar halaman lebih cepat dimuat.
- Ganti placeholder `https://proxy.domainanda.com` dengan URL gateway yang sebenarnya.
- Tambahkan `robots.txt`, `sitemap.xml`, dan `CNAME` (belum ada di server saat ini).
