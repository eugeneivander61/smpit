# Catatan Prompt & Debugging

Dokumen ini mencatat prompt yang dipakai saat membangun website beserta **bug yang benar-benar
terjadi selama proses** lengkap dengan penyebab dan solusinya.

🔗 **Live:** https://eugeneivander61.github.io/smpit/

---

## A. Prompt yang Digunakan

### 1. Perintah awal (scaffolding)
> "di dalam folder ada web bisa kamu lihat — lihat struktur project-nya"

**Hasil:** pembacaan struktur folder (`index.html`, `css/`, `js/`, `img/`) dan analisis seluruh
markup, CSS, serta JavaScript yang sudah ada.

### 2. Prompt analisis
> "apa saja yang bisa diperbaiki di website ini?"

**Hasil:** identifikasi SEO (Open Graph, Twitter Card, JSON-LD), aksesibilitas, dan performa
gambar.

### 3. Prompt debugging — interaksi tab
> "tahfizhnya menghilang setelah diklik"

**Hasil:** penelusuran error di Console (`F12`) → ditemukan ketidaksesuaian nama antara
`data-tab` dan `id` panel.

### 4. Prompt penambahan konten
> "aku pengen nambahin foto di galeri kegiatan santri"

**Hasil:** penambahan entry galeri kategori `kegiatan`, lalu optimasi ukuran gambar agar
ringan untuk web.

### 5. Prompt pengecekan akhir
> "kurang apa saja" (terhadap 6 poin wajib tugas)

**Hasil:** audit terhadap 6 requirement + pembuatan dokumen rencana & catatan ini.

---

## B. Debugging — Bug yang Terjadi & Solusinya

### Bug 1 — Tab "Tahfizh" tidak berfungsi (error JS)

**Pesan error (Console):**
```
Uncaught TypeError: Cannot read properties of null (reading 'classList')
    at HTMLButtonElement.<anonymous> (script.js:187:47)
```

**Penyebab:**
Nama pada tombol tab tidak sama dengan `id` panel-nya — typo huruf akhir:

```html
<!-- tombol: pakai "tahfidz" (z) -->
<button class="tab-btn" data-tab="tahfidz">Tahfizh</button>

<!-- panel: pakai "tahfizh" (h) -->
<div class="tab-panel" id="tahfizh">...</div>
```

`document.getElementById("tahfidz")` mengembalikan **`null`** karena elemen tersebut tidak ada,
lalu `.classList` dipanggil pada `null` → error → panel tidak aktif.

**Solusi:** samakan keduanya menjadi `tahfizh`.
```html
<button class="tab-btn" data-tab="tahfizh">Tahfizh</button>
```

**Pelajaran:** atribut penghubung (`data-*`) harus **cocok persis** dengan target-nya. Juga
selalu guard nilai `getElementById` sebelum pakai:
```js
const panel = document.getElementById(btn.dataset.tab);
if (panel) panel.classList.add("active");
```

---

### Bug 2 — Tombol filter Prestasi tidak muncul

**Gejala:** Section Prestasi hanya menampilkan counter dan kartu, tanpa tombol
**Semua / Internasional / Nasional / Provinsi / Kabupaten-Kota**.

**Penyebab:** class `reveal` dipasang pada wadah filter:

```html
<div class="filter-bar reveal" ...>   <!-- ❌ salah -->
```

CSS `.reveal` memakai `opacity:0` dan baru tampil setelah IntersectionObserver memberi class
`.in-view`. Akibatnya filter bar **tetap transparan** dan tidak terlihat.

**Solusi:** hapus class `reveal` — tombol filter adalah kontrol, seharusnya selalu terlihat.
```html
<div class="filter-bar" ...>          <!-- ✅ benar -->
```

**Pelajaran:** jangan pasang class animasi reveal pada elemen kontrol yang harus selalu
dilihat pengguna.

---

### Bug 3 — Judul kartu Ekstrakurikuler tidak tampak

**Gejala:** 32 kartu ekskul hanya menampilkan ikon, nomor, kategori, dan deskripsi —
**judulnya (`<h3>`) tidak muncul**.

**Penyebab:** fungsi `setupWordAnimation()` membungkus semua `<h2>`/`<h3>` menjadi
`<span class="word-reveal">`, dan CSS-nya:

```css
.word-reveal { opacity: 0; }                        /* awalnya transparan */
.reveal.in-view .word-reveal { opacity: 1; }        /* hanya jadi tampil begini */
```

Kartu prestasi memakai `class="ach-card tilt reveal"` → punya ancestor `.reveal` → judul tampil.
Kartu ekskul hanya `class="eks-card"` → **tidak punya ancestor `.reveal`** → judul selamanya
`opacity:0`.

**Solusi:** tambah aturan CSS yang memanfaatkan `.eks-card.in-view` (sudah otomatis dipasang
oleh fungsi `playEks()`):

```css
.eks-card.in-view .word-reveal { opacity: 1; transform: none; }
```

**Pelajaran:** animasi teks bertahap (`word-reveal`) wajib punya **jalur pemicu** yang jelas,
kalau tidak teks akan hilang permanen.

---

### Bug 4 — Kartu "Kejuaraan Panahan" tampil sangat terlambat

**Penyebab:** typo pada custom property delay animasi — kurang titik desimal:

```html
<article class="ach-card reveal" style="--d:50s">   <!-- ❌ 50 detik! -->
```

Seharusnya `.50s`. Jadi kartu tersebut baru muncul setelah **50 detik**.

**Solusi:** perbaiki menjadi `--d:.50s`.

---

### Bug 5 — Foto Kegiatan Pramuka gagal tampil (broken image)

**Gejala:** kartu "Kegiatan Pramuka" muncul sebagai kotak hitam kosong.

**Penyebab (gabungan 3 masalah):**

1. **Beda kapital ekstensi** — file di folder bernama `pramuka.JPG` / `LWE01452.JPG`,
   sedangkan HTML memanggil `pramuka.jpg` (huruf kecil). GitHub Pages memakai server Linux
   yang **case-sensitive** → `pramuka.jpg` ≠ `pramuka.JPG` → HTTP 404.
2. **Ukuran file terlalu besar** — foto asli 5328 × 4000 px, **12,7 MB**, sementara foto
   galeri lainnya hanya 100–400 KB.
3. **File shortcut** — tersisa file `pramuka-sm.jpg.url` (0,1 KB), bukan gambar.

**Solusi:**
- Rename ke huruf kecil: `pramuka.jpg`
- Buat thumbnail `pramuka-sm.jpg` (lebar 900 px) dan versi lightbox `pramuka.jpg`
  (lebar 1600 px) menggunakan `System.Drawing`
- Hapus file shortcut sampah

**Hasil:** 12,7 MB → **116,8 KB** (thumbnail) + **315,5 KB** (lightbox).

**Pelajaran:** selalu gunakan **huruf kecil untuk nama file web**, dan pisahkan versi
thumbnail dari versi ukuran penuh agar halaman tetap ringan.

---

### Bug 6 — Tag HTML rusak di galeri

**Penyebab:** typo markup — tag ditulis tanpa menutup dengan benar:

```html
<figcaption><strong>Juara Taekwondo</strong><span</span></figcaption>   <!-- ❌ <span</span> -->
```

**Solusi:**
```html
<figcaption><strong>Juara Taekwondo</strong><span></span></figcaption> <!-- ✅ -->
```

---

### Bug 7 — Peringatan `file://` saat dibuka langsung

**Pesan:**
```
Unsafe attempt to load URL file:///D:/koding/index.html from frame...
'file:' URLs are treated as unique security origins.
```

**Penyebab:** halaman dibuka langsung dari disk (`file://`), bukan via server HTTP.
Browser memperlakukan tiap file sebagai origin berbeda sehingga bagian tertentu (iframe,
slideshow, font) tidak berjalan normal.

**Solusi:** jalankan lewat server lokal (`127.0.0.1:8123`) atau deploy ke hosting
(GitHub Pages). Peringatan ini bukan error fatal, tapi praktik terbaik adalah selalu uji
via HTTP.

---

### Bug 8 — Server lokal gagal jalan

**Pesan error:**
```
Python tidak ditemukan; jalankan tanpa argumen untuk diinstal dari Microsoft Store...
```

**Penyebab:** `python -m http.server 8000` tidak bisa dijalankan karena Python tidak
terinstal / tidak ada di PATH. Node.js juga tidak tersedia.

**Solusi:** pakai server PowerShell berbasis `.NET HttpListener`
(`_tools/server.ps1`) yang berjalan di `http://127.0.0.1:8123/`.

---

## C. Ringkasan Perubahan File

| File | Perubahan |
|---|---|
| `index.html` | Fix `data-tab="tahfizh"` (Bug 1), hapus `reveal` pada filter prestasi (Bug 2), tambah entry galeri Pramuka, fix typo `--d:50s` (Bug 4), fix `<span</span>` (Bug 6), cache-buster `script.js?v=2` |
| `css/style.css` | Tambah `.eks-card.in-view .word-reveal` (Bug 3) |
| `js/script.js` | Ditulis ulang dengan null checks pada seluruh `getElementById` |
| `img/galeri/pramuka.jpg` | Versi lightbox 1600 px (315 KB) |
| `img/galeri/pramuka-sm.jpg` | Thumbnail 900 px (117 KB) |
| `README.md` | Catatan rencana website (dokumen ini bagian A) |
| `CATATAN.md` | Catatan prompt & debugging (dokumen ini) |

---

