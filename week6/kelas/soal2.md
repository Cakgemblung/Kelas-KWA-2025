# Write-up: Cross-Site Imaging Challenge (OWASP Juice Shop)

## Ringkasan Tantangan

- **Judul:** Cross-Site Imaging
- **Kategori:** Cross-Site Request Forgery (CSRF) / Injection
- **Tingkat Kesulitan:** ⭐⭐⭐⭐⭐ (5/6)
- **Tujuan:** Mengeksploitasi kerentanan pada penanganan URL untuk menyuntikkan dan menampilkan gambar dari sumber eksternal pada halaman yang seharusnya hanya memuat sumber daya internal.

---

## Tools yang Digunakan

- **Web Browser:** Untuk menavigasi aplikasi dan memanipulasi parameter URL.
- **Developer Tools (F12):** Untuk menginspeksi kode sumber JavaScript dan memahami logika aplikasi di sisi klien.

---

## Metodologi dan Solusi

Metodologi untuk menyelesaikan tantangan ini melibatkan analisis kode *frontend* untuk menemukan vektor injeksi, diikuti dengan pembuatan *payload* yang menggabungkan kerentanan *Directory Traversal* dan penyalahgunaan fitur *Internal Redirect*.

### Langkah 1: Analisis Kode dan Identifikasi Vektor

1.  **Akses Halaman Target:** Pertama, navigasi ke halaman **Deluxe Membership**. Halaman ini menampilkan sebuah gambar logo yang dimuat secara dinamis.
2.  **Inspeksi Kode JavaScript:** Dengan menggunakan *Developer Tools*, buka *file* `main.js` atau *file* JavaScript relevan lainnya. Cari kode yang bertanggung jawab untuk memuat gambar di halaman tersebut. Ditemukan bahwa sumber gambar dikendalikan oleh sebuah atribut, dan nilainya dapat ditimpa (*override*) melalui parameter URL.
3.  **Penemuan Parameter Injeksi:** Ditemukan bahwa parameter URL `testDecal` secara langsung memengaruhi path gambar yang akan dimuat, menjadikannya titik masuk (vektor) yang ideal untuk serangan.

### Langkah 2: Konstruksi Payload Eksploitasi

Tantangan utama adalah server membatasi sumber daya hanya dari *path* internal. Untuk melewati batasan ini, dua kerentanan harus dieksploitasi secara bersamaan:

1.  **Directory Traversal:** Karakter `../` digunakan untuk menavigasi ke direktori induk. Dengan mengulanginya (`../../../../`), kita bisa keluar dari direktori gambar yang diizinkan dan mencapai direktori *root* dari server web.
2.  **Penyalahgunaan Internal Redirect:** Aplikasi memiliki *endpoint* `/redirect` yang dirancang untuk mengarahkan pengguna ke URL lain. *Endpoint* ini tidak memvalidasi apakah tujuan pengalihan adalah URL internal atau eksternal.

Kombinasi keduanya memungkinkan kita untuk "menipu" aplikasi agar membuat permintaan ke *endpoint redirect* internal, yang kemudian akan mengarahkannya untuk memuat konten dari domain eksternal.

### Langkah 3: Implementasi Serangan

*Payload* akhir dirancang untuk dimasukkan ke dalam parameter `testDecal` untuk memaksa aplikasi memuat gambar eksternal.

**URL Serangan Akhir:**```url
http://127.0.0.1:3000/#/deluxe-membership?testDecal=../../../../redirect?to=https://dummyimage.com/600x400/fff&fx=https://github.com/bkimminich/juice-shop

```
- **Cara Kerja Payload:**
  - `testDecal=../../../../` : Memanipulasi *path* untuk keluar dari direktori yang diharapkan.
  - `redirect?to=https://dummyimage.com/...` : Mengarahkan server untuk mengakses *endpoint* pengalihannya sendiri dan memuat gambar dari URL eksternal yang ditentukan.

Setelah mengakses URL ini, gambar dari `dummyimage.com` berhasil dimuat dan ditampilkan pada halaman Deluxe Membership, menandakan bahwa tantangan Cross-Site Imaging telah diselesaikan.
```

## Analisis Akar Masalah (Root Cause)

Kerentanan ini muncul dari kombinasi dua kelemahan keamanan:

1.  **Penanganan Path yang Tidak Aman:** Aplikasi gagal melakukan sanitasi atau normalisasi pada *input* yang diterima melalui parameter `testDecal`, sehingga memungkinkan serangan **Directory Traversal**.
2.  **Redirect yang Tidak Tervalidasi (Open Redirect):** Fitur `/redirect` tidak memiliki *whitelist* atau mekanisme validasi yang memadai, sehingga dapat disalahgunakan untuk mengarahkan permintaan ke domain eksternal mana pun.



## Rekomendasi Remediasi

Untuk memperbaiki kerentanan ini, langkah-langkah berikut harus diterapkan:

- **Validasi Input Secara Ketat:** Semua *input* dari pengguna yang memengaruhi *path file* atau URL harus divalidasi dengan ketat. Gunakan *allowlist* untuk hanya mengizinkan karakter dan format yang aman. Tolak *input* yang mengandung urutan karakter seperti `../`.
- **Batasi Tujuan Redirect:** Implementasikan *whitelist* di sisi server untuk *endpoint redirect*. Izinkan pengalihan hanya ke URL atau domain yang telah disetujui secara eksplisit.
- **Gunakan Absolute Path:** Saat mereferensikan sumber daya internal, selalu gunakan *absolute path* yang dimulai dari *root* dokumen aplikasi. Hindari penggunaan *relative path* yang rentan terhadap manipulasi.


