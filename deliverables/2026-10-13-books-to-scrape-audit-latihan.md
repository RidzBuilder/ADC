# Laporan Audit Digital — Books to Scrape
**LATIHAN — bukan audit klien**  
Mode M0 · read-only · bukti observasi situs E2

## Intake brief
- Target: https://books.toscrape.com/
- Tujuan: latihan alur intake, audit bukti publik, laporan, dan uji gagal.
- Ruang lingkup: katalog utama, katalog halaman 2, detail A Light in the Attic, dan forced-failure page 51.
- Di luar cakupan: login, checkout, analytics, performa, aksesibilitas, SEO teknis lengkap.

## Metode
Tiga halaman yang sudah terbaca ditinjau. Firecrawl dipakai satu kali untuk page-51.html setelah fetch web gagal mendapatkan status HTTP. Tidak ada fetch tambahan. Observasi langsung diberi E2; artefak repo maksimal E3 setelah read-back.

## Temuan Situs

### S-01 — Pagination katalog
**Prioritas:** Sedang · **Keyakinan:** Tinggi  
**Bukti:** https://books.toscrape.com/ ; https://books.toscrape.com/catalogue/page-2.html  
Katalog utama menunjukkan hasil 1–20 dari 1.000; halaman 2 menunjukkan 21–40 dari 1.000. Navigasi menyatakan 50 halaman.

### S-02 — Peringatan data demo
**Prioritas:** Sedang · **Keyakinan:** Tinggi  
**Bukti:** https://books.toscrape.com/ ; https://books.toscrape.com/catalogue/page-2.html ; https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html  
Situs menyatakan harga dan rating acak serta tidak bermakna sebagai data bisnis nyata.

## Belum diuji / UNKNOWN
- Meta description ketiga halaman: UNKNOWN.
- H1 ketiga halaman: UNKNOWN.
- Judul HTML halaman utama dan katalog halaman 2: UNKNOWN dari bukti HTML mentah yang terverifikasi di trace.
- Performa, aksesibilitas, SEO lengkap, sitemap/robots, checkout, analytics: belum diuji; bukan temuan negatif.

## Catatan Pengembangan ADC
### D-01 — Uji gagal URL halaman 51
**Status:** PASS untuk forced-failure test · **Keyakinan:** Tinggi  
**Bukti:** https://books.toscrape.com/catalogue/page-51.html  
HTTP 404 Not Found; scrapeId 01a1330d-22d6-717f-944f-267523d84aae. Ini hasil uji ADC, bukan temuan kerusakan situs.

### D-02 — Batas provider
Fetch web gagal menetapkan status HTTP; sistem berpindah ke Firecrawl tanpa izin terlebih dahulu, melanggar batas awal. Pengguna menerima satu fetch secara retroaktif hanya untuk bukti 404. Selanjutnya perpindahan provider wajib meminta izin; provider yang sudah disetujui boleh dipakai dalam ruang lingkup dan kuota tanpa izin per fetch.

### D-03 — Kredit Firecrawl (OBSERVATION)
Saldo 978/1000; riwayat menunjukkan Connected app=32 dan Default=5 sejak 2026-10-01. Periode saldo mulai 2026-09-26. Asal selisih 21 kredit sebelum fetch dan riwayat URL: UNKNOWN. Pengguna memeriksa dashboard. Tidak ada fetch tambahan.

## Permintaan persetujuan
Tinjau draf dan diff branch. Persetujuan draf tidak berarti izin merge. Merge tetap tidak diizinkan tanpa keputusan terpisah.
