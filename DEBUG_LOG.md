# DEBUG_LOG Latihan P10 (Katalog Karya)

Semua perbaikan dikerjakan pada cabang `fix/katalog`. Setiap bug ditangani
dengan urutan yang sama: mengamati gejala, menulis dugaan awal (hipotesis),
memeriksa DevTools, mencari penyebab pada kode, memperbaiki, lalu menguji ulang
di browser.

## G1: Logo navbar tidak muncul (404)

Ikon logo pada navbar tampil rusak. Tab Network mencatat permintaan
`img/logo.svg` dengan status 404.

Hipotesis pertama adalah berkas SVG-nya rusak. Hipotesis tersebut gugur karena
`assets/logo.svg` dapat dibuka langsung di browser dengan status 200.

Penyebabnya ada pada `index.html:13`, yaitu atribut `src` mengarah ke folder
`img/`, padahal berkas logo disimpan di folder `assets/`.

Perbaikan aset dikerjakan pada cabang yang sama dalam dua commit terpisah.
Commit `1d2d0f3` (`chore`) mengganti nama `assets/logo.svg` menjadi
`assets/logo-katalog.svg` sesuai aturan huruf kecil dan tanda hubung. Commit
`4509851` (`fix`) mengubah `src` pada `index.html:13` agar menunjuk ke berkas
tersebut, sekaligus memasang gambar yang sama sebagai favicon supaya browser
tidak lagi meminta `favicon.ico`.

Setelah halaman dimuat ulang, tab Network tidak lagi menampilkan 404.

## G2: Kartu karya tersusun satu kolom

Kartu karya tampil memanjang ke bawah satu per satu walaupun lebar layar
mencukupi untuk beberapa kolom.

Pada tab Elements, elemen `#galeri` hanya memiliki `class="g-3"`. Hipotesis
awalnya kelas `g-3` hilang, tetapi hipotesis itu gugur karena kelas tersebut
hanya mengatur jarak antar elemen dan bukan pembentuk grid.

Penyebabnya, kelas `row` belum ada pada `index.html:34`. Setelah diubah menjadi
`class="row g-3"`, kartu tersusun tiga kolom pada layar lebar dan tetap rapi pada
layar sempit.

## G3: Filter kategori tidak berfungsi

Ketika kategori dipilih, daftar karya tidak berubah dan Console menampilkan
pesan kesalahan berikut.

```
Uncaught TypeError: Cannot read properties of null (reading 'addEventListener')
    at main.js:54:43
```

Pesan tersebut menandakan `querySelector` mengembalikan `null`. Hipotesis awal
adalah elemen belum siap atau urutan pemuatan script (`defer`) salah. Hipotesis
itu gugur setelah elemen lain diperiksa dan semuanya ditemukan, sehingga DOM
sudah siap.

Penyebab sebenarnya ada pada `main.js:54`, yaitu selektor `#filter-kategori`,
sedangkan id pada HTML adalah `filterKategori`. Selektor diganti memakai
variabel `filterKategori` yang sudah tersedia. Setelah itu semua kategori dapat
menyaring dan Console bersih.

## G4: Keterangan jumlah karya tidak ikut berubah

Setelah penyaringan, jumlah kartu berkurang menjadi dua, tetapi keterangan
masih menampilkan "6 dari 6".

Tidak ada hipotesis yang perlu diuji pada bug ini karena penyebabnya langsung
terlihat pada kode. Pada `main.js:38` angka pertama masih diambil dari
`KARYA.length`, yaitu panjang seluruh data. Angka tersebut diganti dengan
`hasil.length` agar mengikuti jumlah hasil penyaringan.

## G5: Tombol Detail tidak berfungsi setelah pencarian

Tombol Detail bekerja saat halaman pertama kali dimuat, tetapi tidak merespons
setelah pencarian atau penyaringan dilakukan.

Hipotesis awal adalah listener tidak terpasang sama sekali, tetapi hipotesis itu
gugur karena tombol berfungsi saat halaman baru dimuat. Pada tab Event
Listeners, listener hanya terpasang pada tombol hasil render pertama. Fungsi
`replaceChildren()` mengganti seluruh tombol, sehingga listener yang lama ikut
hilang.

Perbaikannya memakai delegasi event: listener dipasang sekali pada `#galeri`,
lalu tombol yang diklik dicari dengan `closest('button[data-id]')`
(`main.js:41-50`). Pengujian dilakukan sebelum dan sesudah penyaringan, dan
tombol Detail berfungsi pada kedua kondisi.

## G6: Pencarian tidak menemukan judul berhuruf kapital

Kata kunci "animasi" tidak menghasilkan karya, padahal terdapat karya berjudul
"Animasi ...". Jika ditulis "Animasi", hasilnya dua kartu.

Hipotesis awal adalah data judulnya kosong, tetapi setelah diperiksa datanya
lengkap. Penyebabnya ada pada `main.js:14`, yaitu perbandingan `includes()`
dilakukan tanpa mengubah kedua sisi menjadi huruf kecil.

Setelah ditulis `k.judul.toLowerCase().includes(kata.toLowerCase())`, kata kunci
"animasi" maupun "Animasi" sama-sama menghasilkan dua kartu.

## Ringkasan

| Bug | Alat | Berkas:baris | Commit |
|---|---|---|---|
| G1 | Network | index.html:13 | `4509851` |
| G2 | Elements | index.html:34 | `1bc4c61` |
| G3 | Console | main.js:54 | `bacad4e` |
| G4 | Elements | main.js:38 | `e03d85c` |
| G5 | Event Listeners | main.js:41-50 | `83b1d45` |
| G6 | Console | main.js:14 | `d7808b1` |

Dari enam bug, lima hipotesis ternyata gugur (G1, G2, G3, G5, dan G6),
sedangkan pada G4 penyebabnya langsung terlihat pada kode.
