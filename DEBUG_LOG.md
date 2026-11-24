# Catatan Investigasi Bug — Latihan P10 (Katalog Karya)

## G1 — Logo di Navbar Tidak Tampil
- **Langkah reproduksi:** Buka `index.html` di browser dan periksa tab Network DevTools.
- **Hipotesis:**
  1. Berkas `logo.svg` korup atau tidak valid *(gugur: berkas SVG valid dan dapat dibuka)*.
  2. Path `src="img/logo.svg"` pada elemen `<img>` tidak sesuai dengan lokasi berkas di `assets/logo.svg` *(terbukti)*.
- **Bukti DevTools:** Tab Network menunjukkan permintaan ke `img/logo.svg` berstatus `404 Not Found`.
- **Perbaikan:** `index.html` (bagian `<img>` navbar) diubah dari `src="img/logo.svg"` menjadi `src="assets/logo.svg"`.
- **Uji ulang:** Muat ulang halaman; status `404` hilang dan logo tampil pada navbar.

## G2 — Kartu Karya Tersusun Satu Kolom di Layar Lebar
- **Langkah reproduksi:** Buka halaman di layar lebar (> 992 px) dan periksa struktur elemen galeri di tab Elements.
- **Hipotesis:**
  1. Kelas kolom pada kartu (`col-lg-4`) salah *(gugur: kelas `col-12 col-sm-6 col-lg-4` sudah sesuai grid Bootstrap)*.
  2. Kontainer `#galeri` belum memiliki kelas `row` dari Bootstrap *(terbukti)*.
- **Bukti DevTools:** Tab Elements menunjukkan `#galeri` hanya memiliki kelas `g-3` tanpa `row`, sehingga *grid* horizontal Bootstrap tidak aktif.
- **Perbaikan:** `index.html` pada `<div id="galeri" class="g-3"></div>` diubah menjadi `<div id="galeri" class="row g-3"></div>`.
- **Uji ulang:** Di lebar layar > 992 px, kartu tampil tersusun menyamping sesuai kolom grid.

## G3 — Filter Kategori Tidak Berubah & Muncul TypeError di Console
- **Langkah reproduksi:** Buka tab Console DevTools lalu pilih kategori `"Video"` pada dropdown filter.
- **Hipotesis:**
  1. Nilai `<option>` pada `<select>` tidak cocok dengan properti `kategori` di `data.js` *(gugur: nilai opsi `"Video"` sama persis dengan data)*.
  2. Selector `document.querySelector('#filter-kategori')` mengembalikan `null` karena ID elemen adalah `filterKategori` *(terbukti)*.
- **Bukti DevTools:** Tab Console menampilkan `TypeError` saat `addEventListener('change', ...)` dipanggil pada hasil `querySelector` yang `null`.
- **Perbaikan:** `main.js` pada baris `document.querySelector('#filter-kategori')` diubah menjadi `document.querySelector('#filterKategori')`.
- **Uji ulang:** Memilih `"Video"` menyaring daftar karya tanpa error di Console.

## G4 — Teks Jumlah Karya Tidak Berubah Saat Disaring
- **Langkah reproduksi:** Ketik `"Video"` pada kotak pencarian dan amati teks `jumlah`.
- **Hipotesis:**
  1. Fungsi `saring()` tidak mengembalikan array yang sudah tersaring *(gugur: `saring()` mengembalikan data yang sudah difilter)*.
  2. `jumlah.textContent` di `tampil()` menggunakan `KARYA.length` alih-alih `hasil.length` *(terbukti)*.
- **Bukti DevTools:** Breakpoint/pemeriksaan pada `tampil()` memperlihatkan `hasil.length` berubah, tetapi `jumlah.textContent` tetap memakai `KARYA.length`.
- **Perbaikan:** `main.js` pada `tampil()` diubah menjadi `Menampilkan ${hasil.length} dari ${KARYA.length} karya`.
- **Uji ulang:** Saat mengetik `"Video"`, teks jumlah berubah menjadi `Menampilkan 2 dari 6 karya`.

## G5 — Tombol Detail Mati Setelah Melakukan Pencarian
- **Langkah reproduksi:** Ketik `"Animasi"` di kotak pencarian, lalu klik tombol **Detail** pada kartu hasil pencarian.
- **Hipotesis:**
  1. Atribut `data-id` hilang saat kartu dibuat ulang *(gugur: `data-id="${k.id}"` tetap terpasang pada tombol)*.
  2. `pasangDetail()` hanya dijalankan sekali saat awal muat halaman, padahal `galeri.replaceChildren(...)` membuat elemen tombol baru saat render ulang *(terbukti)*.
- **Bukti DevTools:** Pemeriksaan Event Listeners pada tombol baru setelah pencarian menunjukkan listener `click` tidak terpasang.
- **Perbaikan:** `main.js` pada fungsi `tampil()` dipanggil `pasangDetail();` setelah `jumlah.textContent` diperbarui.
- **Uji ulang:** Setelah mengetik `"Animasi"`, klik tombol **Detail** menampilkan kotak informasi detail karya.

## G6 — Pencarian Tidak Menemukan Hasil Saat Menggunakan Huruf Kecil
- **Langkah reproduksi:** Ketik `"animasi"` (huruf kecil semua) di kotak pencarian.
- **Hipotesis:**
  1. Event `input` pada `#cari` tidak terhubung ke `tampil` *(gugur: ketikan `"Animasi"` memicu pembaruan daftar)*.
  2. `k.judul.includes(kata)` bersifat *case-sensitive* *(terbukti)*.
- **Bukti DevTools:** Breakpoint di `saring()` menunjukkan `kata` bernilai `"animasi"` sementara `k.judul` diawali `"Animasi"`, sehingga `includes` bernilai `false`.
- **Perbaikan:** `main.js` pada `saring()` menyamakan huruf dengan `.toLowerCase()` pada `kata` dan `k.judul`.
- **Uji ulang:** Mengetik `"animasi"` menampilkan karya dengan judul `"Animasi..."`.
