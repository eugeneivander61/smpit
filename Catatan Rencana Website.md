# SMP IT Ibnu Abbas Klaten — Website Profil

Website profil satu halaman (single-page) untuk **SMP IT Ibnu Abbas Klaten**, sekolah Islam
terpadu di bawah naungan **PPTQ Ibnu Abbas Klaten**, Belangwetan, Klaten Utara, Jawa Tengah.

🔗 **Live:** https://eugeneivander61.github.io/smpit/
📦 **Repo:** https://github.com/eugeneivander61/smpit (public)

---

## 1. Tujuan Website

- Menjadi profil resmi sekolah yang bisa diakses calon orang tua & santri.
- Menampilkan identitas sekolah, program pendidikan, kegiatan, prestasi, dan fasilitas.
- Dibuat **mobile-friendly** dan ringan, tanpa framework/library eksternal.

## 2. Target Pengguna

| Pengguna | Kebutuhan |
|---|---|
| Calon orang tua santri | Info program, prestasi, fasilitas |
| Santri aktif | Info ekskul & kegiatan |
| Panitia / sekolah | Media promosi & dokumentasi resmi |

## 3. Struktur Section

| # | Section | ID | Isi |
|---|---|---|---|
| 1 | Beranda (Hero) | `#beranda` | Judul, tagline, statistik (32 kegiatan, 19 putra, 13 putri), tombol CTA |
| 2 | Tentang | `#tentang` | Profil sekolah, visi, harmonisasi pesantren–sekolah |
| 3 | Pilar Pendidikan | `#pilar` | Tab **Tahfizh · Akademik · Akhlak** |
| 4 | Ekstrakurikuler | `#ekskul` | Switch Putra/Putri + filter bidang, 32 kartu ekskul |
| 5 | Prestasi | `#prestasi` | Counter prestasi + filter tingkat, 6 kartu |
| 6 | Kampus | `#kampus` | 2 kampus (putri & putra) |
| 7 | Galeri | `#galeri` | Tab Fasilitas/Kegiatan, lightbox, slideshow background |

**Total: 7 section** — semuanya punya menu navigasi aktif (scrollspy).

## 4. Struktur File

```
koding/
├── index.html          # markup seluruh halaman + SEO
├── css/
│   └── style.css       # design system, animasi, responsif
├── js/
│   └── script.js       # interaksi & motion (vanilla JS)
├── img/
│   ├── galeri/         # foto fasilitas & kegiatan (penuh + thumbnail -sm)
│   ├── logo-smpit.png
│   ├── logo-pptq.png
│   └── og-image.jpg
├── robots.txt
├── sitemap.xml
└── _tools/             # script bantu (optimasi gambar, server lokal)
```

## 5. Teknologi

- **HTML5** — semantic sectioning, ARIA (`role="tab"`, `aria-selected`, `aria-modal`)
- **CSS3** — custom properties, `clamp()`, `grid`, `flexbox`, `@media` responsif, `prefers-reduced-motion`
- **Vanilla JavaScript** — tanpa framework, tanpa library eksternal
- **SEO** — meta description, Open Graph, Twitter Card, JSON-LD (`schema.org/School`), canonical, `robots.txt`, `sitemap.xml`

## 6. Fitur Utama

- Preloader & scroll progress bar
- Navbar blur + sembunyi saat scroll ke bawah
- Menu hamburger untuk mobile
- Scrollspy navigasi aktif
- Reveal on scroll (IntersectionObserver)
- Word-by-word heading animation
- Animated counter
- Tab pilar dengan sliding pill
- Filter ekskul (gender + bidang) dengan animasi stagger
- Filter prestasi berdasarkan tingkat
- 3D tilt card & magnetic button (desktop)
- Galeri: slideshow background + lightbox (navigasi keyboard)
- Parallax ornament & section
- Smooth scroll dengan offset navbar

## 7. Aksesibilitas & Performa

- `loading="lazy"` + `width`/`height` eksplisit pada gambar (cegah layout shift)
- Gambar galeri dipisah versi penuh (`lightbox`) & thumbnail (`-sm`) agar ringan
- `prefers-reduced-motion` dihormati — animasi dimatikan bila pengguna memintanya
- Kontras warna terbaca, fokus state, dan teks alternatif (`alt`) pada gambar

## 8. Rencana Pengembangan Berikutnya

- [ ] Optimasi foto pramuka & unggah dokumentasi kegiatan lainnya
- [ ] Halaman terpisah untuk PPDB / pendaftaran
- [ ] Formulir kontak
- [ ] Bahasa Inggris (multi-bahasa)
