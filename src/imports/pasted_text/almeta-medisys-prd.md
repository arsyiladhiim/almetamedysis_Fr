# MASTER PRD — Website Almeta Medisys

> Versi: 1.0 · Status: Draft disetujui · Terakhir diubah: 2026-09-02
> Dokumen induk. Dokumen teknis lain (arsitektur, DB, SEO, dll.) mengacu ke sini.

---

## 1. Latar Belakang

**Almeta Medisys** adalah anak perusahaan **PT Marthys Orthopaedic Indonesia** (produsen implan ortopedi pertama di Indonesia, berdiri 6 Des 1998, pabrik Prigen–Pasuruan, 7000 m², investasi ±Rp60 M). Marthys berperan sebagai **produsen**; Almeta Medisys diposisikan sebagai entitas **distribusi / alkes umum** yang memasarkan produk Marthys dan brand terkait.

Situs induk (marthysorthopaedic.com) saat audit (Sep 2026) **offline/tidak merespons**; konten dipulihkan dari arsip Wayback Machine (Jul 2025) — lihat `CONTENT_INVENTORY.md` dan `docs/references/`.

## 2. Tujuan Produk

1. **Presence** — company profile kredibel untuk Almeta Medisys, terhubung riwayat Marthys.
2. **Katalog** — katalog produk online (listing + detail) dan katalog PDF unduhan.
3. **Lead capture** — form Request for Quotation (RFQ) dan pendaftaran distributor.
4. **Edukasi/SEO** — blog artikel untuk otoritas domain & trafik organik.

## 3. Target Audiens

| Segmen | Kebutuhan |
|---|---|
| Rumah sakit / instansi (pengadaan, e-katalog) | Katalog, sertifikasi (TKDN, AKD, e-katalog Kemenkes), RFQ |
| Distributor alkes | Program kemitraan, katalog PDF, kontak cepat |
| Dokter bedah ortopedi | Spesifikasi produk per seri/material |
| Umum / mesin pencari | Profil perusahaan, artikel |

## 4. Keputusan Terkunci (Jangan diubah tanpa revisi PRD)

- **Monolith penuh**: Laravel 13, PHP 8.4, Blade + Tailwind CSS 4 + Alpine.js. **Tanpa** Inertia/Vue/React.
- **Database**: PostgreSQL 18 (Docker).
- **Admin**: Filament panel (CRUD produk, kategori, brand, distributor, artikel, inquiry, media).
- **Bahasa**: dual ID/EN, route prefix `/{locale}`, switcher di navbar.
- **Non-goals**: e-commerce, keranjang, pembayaran, akun pelanggan, SSO, API publik.

## 5. Ruang Lingkup Fitur

### FR-01 Home
Hero slider (produk unggulan), value props (cakupan TKDN, e-katalog Kemenkes, AKD, dukungan pabrik Marthys), grid brand, strip distributor, highlight artikel, CTA RFQ.

### FR-02 About
Profil Almeta Medisys, relasi dengan PT Marthys Orthopaedic Indonesia, timeline/legalitas, nilai perusahaan.

### FR-03 Products (katalog online)
- Listing dengan filter: kategori/seri, material (SS/Ti), jenis (locking / non-locking), brand.
- Detail produk: nama, kode, spesifikasi, material, gambar, produk terkait.
- Data dikelola admin (Filament), diseed konten awal.

### FR-04 Catalog PDF
Halaman unduh/viewer PDF katalog (viewer embed + tombol download). File diupload via admin.

### FR-05 Distributors
Daftar distributor (logo + nama + wilayah), CTA "Jadi Distributor" → form.

### FR-06 Blog
Listing artikel (pagination), detail artikel, kategori/tag sederhana, artikel terkait.

### FR-07 Contact + RFQ
- Info kontak (alamat, telp, WA, email, jam kerja), peta.
- Form **RFQ**: nama, instansi, email, telp/WA, produk/minat, pesan → email notifikasi (queued) + fallback deep-link WhatsApp; tersimpan di DB (`inquiries`).
- Form **Jadi Distributor**: fields serupa + wilayah.

### FR-08 Admin Dashboard (Filament)
- CRUD: Product, Category, Brand, Distributor, Post, Inquiry (inbox), CatalogFile, DistributorApplication.
- Auth khusus admin; dashboard widget: jumlah inquiry baru, produk, artikel.

## 6. Non-Functional Requirements

| Area | Target |
|---|---|
| Performa | LCP < 2.5s, INP < 200ms, CLS < 0.1 (Lighthouse mobile) |
| SEO | hreflang id/en, sitemap.xml, robots.txt, schema.org Organization + Product, OG/Twitter meta |
| Keamanan | CSRF (bawaan), rate limit form, validasi server-side, tidak ada data sensitif publik, header keamanan |
| Aksesibilitas | WCAG AA: kontras ≥ 4.5:1, navigasi keyboard, alt text, focus visible |
| i18n | Semua teks UI via `lang/{id,en}.json`; konten DB bilingual (kolom `*_id` / `*_en` atau JSON translatable) |
| Browser | 2 versi terakhir Chrome/Firefox/Safari/Edge; mobile 360px+ |

## 7. Keputusan YAGNI (ditolak eksplisit, tambah nanti bila perlu)

- Inertia/Vue/React → Blade + Alpine cukup.
- Multi-vendor, cart, checkout → bukan e-commerce.
- API publik → belum ada konsumen.
- Multi-admin roles kompleks → satu role admin dulu.
- Daftar harga publik → mengikuti praktik industri (via RFQ).

## 8. Metrik Sukses (fase 1)

- Situs live di domain produksi, SSL aktif, uptime 99%+.
- ≥ 20 produk terdata, katalog PDF terunduh.
- Form RFQ/distributor berfungsi end-to-end (email tiba + tersimpan).
- Lighthouse: Perf ≥ 80, SEO ≥ 95, A11y ≥ 90, Best Practices ≥ 90.
- Artikel awal ≥ 3 terbit bilingual.

## 9. Asumsi & Dependensi

- Aset final (logo, foto, PDF katalog, artikel) dari klien — checklist di `ASSETS_NEEDED.md`.
- Konten warisan dari audit Marthys boleh dipakai sebagai dasar copy (perusahaan satu grup).
- Kredensial SMTP/WA gateway disediakan saat deployment.

## 10. Struktur Dokumen

| File | Isi |
|---|---|
| `ARCHITECTURE.md` | Struktur aplikasi Laravel, pola, alur request |
| `TECH_STACK.md` | Versi pinned + alasan |
| `DATABASE_SCHEMA.md` | ERD + definisi tabel |
| `API_ROUTES.md` | Daftar route publik & admin |
| `I18N.md` | Strategi bilingual |
| `UI_UX.md` | Design direction + standar komponen |
| `SEO.md` | Spesifikasi SEO |
| `CONTENT_INVENTORY.md` | Audit konten situs Marthys |
| `DEPLOYMENT.md` | Docker, env, CI/CD, bootstrapping produksi |
| `DEV_SETUP.md` | Cara run lokal & troubleshooting |
| `ROADMAP.md` | Fase & kriteria done |
| `ASSETS_NEEDED.md` | Checklist aset dari klien |
| `references/` | Snapshot HTML arsip marthysorthopaedic.com |