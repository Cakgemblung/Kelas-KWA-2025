# Lab Write-Up: Unprotected Admin functionality with unpredictable URL

- **Platform:** PortSwigger Web Security Academy
- **Tingkat Kesulitan:** Apprentice
- **Link Lab:** `https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality-unpredictable-url`

---

##  Ringkasan

Lab ini merupakan variasi dari kerentanan panel admin yang tidak terproteksi. Tantangan utamanya adalah lokasi (URL) dari panel admin tersebut tidak dapat ditebak dan tidak diungkapkan di lokasi umum seperti `robots.txt`. Sebaliknya, URL tersebut bocor di dalam kode JavaScript pada halaman utama aplikasi. Tujuan akhirnya tetap sama: mengakses panel admin dan menghapus pengguna `carlos`.

---

##  Analisis Kerentanan

Kerentanan pada lab ini terdiri dari dua bagian:

1.  **Broken Access Control:** Sama seperti lab sebelumnya, panel admin tidak memiliki mekanisme autentikasi atau otorisasi. Siapa pun yang mengetahui URL-nya dapat langsung mengakses fungsionalitas administratif.
2.  **Information Disclosure (Bocoran Informasi):** Aplikasi membocorkan URL panel admin yang seharusnya rahasia di dalam kode JavaScript yang dikirimkan ke browser klien. Meskipun ada logika JavaScript untuk menyembunyikan tautan dari antarmuka pengguna, URL itu sendiri tetap ada dalam *source code* dan dapat dengan mudah ditemukan.

---

##  Tools yang Digunakan

* Web Browser (dengan fitur "View Page Source" atau "Developer Tools")

---

##  Langkah-langkah Penyelesaian (Walkthrough)

### 1. **Pengintaian: Analisis Kode Sumber Klien (Client-Side)**

Karena URL tidak dapat diprediksi, langkah pertama adalah memeriksa aset *front-end* yang dikirimkan oleh server, terutama kode HTML dan JavaScript.

1.  Saya membuka halaman utama lab.
2.  Saya menggunakan fitur "View Page Source" (Ctrl+U) di browser untuk memeriksa kode HTML mentah dari halaman tersebut.
3.  Dengan mencari kata kunci seperti `admin`, saya menemukan blok kode JavaScript yang menarik:

    ```javascript
    var isAdmin = false;
    if (isAdmin) {
       var topLinksTag = document.getElementsByClassName("top-links")[0];
       var adminPanelTag = document.createElement('a');
       adminPanelTag.setAttribute('href', '/admin-ao1wk4');
       adminPanelTag.innerText = 'Admin panel';
       topLinksTag.append(adminPanelTag);
       var pTag = document.createElement('p');
       pTag.innerText = '|';
       topLinksTag.appendChild(pTag);
    }
    </script>
    ```

4.  Dari analisis kode di atas, jelas bahwa:
    * Variabel `isAdmin` diatur ke `false`, sehingga logika untuk menampilkan tautan "Admin panel" tidak pernah berjalan.
    * Namun, path URL yang sensitif, yaitu `/admin-ao1wk4`, tertulis secara eksplisit di dalam kode. Inilah informasi yang saya butuhkan.

### 2. **Eksploitasi: Mengakses Panel dan Menghapus Pengguna**

Setelah path rahasia ditemukan, langkah eksploitasi menjadi sangat mudah.

1.  Saya menggabungkan *hostname* lab dengan path yang ditemukan: `https://<ID-LAB>.web-security-academy.net/admin-ao1wk4`.
2.  Saya mengakses URL tersebut di browser dan berhasil masuk ke panel admin tanpa autentikasi.
3.  Di dalam panel, saya menemukan pengguna **carlos** dan mengklik tombol **Delete**.
4.  Lab berhasil diselesaikan, ditandai dengan munculnya banner konfirmasi.

---

##  Rekomendasi Mitigasi

1.  **Jangan Menyimpan Data Sensitif di Sisi Klien:** URL, kunci API, atau logika bisnis yang sensitif tidak boleh disimpan atau diekspos dalam kode JavaScript, HTML, atau CSS. Data tersebut seharusnya hanya berada dan diproses di sisi server.
2.  **Terapkan Kontrol Akses di Sisi Server:** Ini adalah mitigasi yang paling penting. Setiap permintaan ke endpoint `/admin-*` harus divalidasi di server untuk memastikan bahwa pengguna telah terautentikasi **dan** memiliki otorisasi (peran admin) untuk mengakses sumber daya tersebut.
3.  **Hindari Logika Keamanan di Sisi Klien:** Jangan pernah mengandalkan JavaScript untuk menyembunyikan atau menonaktifkan fungsionalitas sebagai satu-satunya lapisan keamanan. Logika seperti `if (isAdmin)` di sisi klien sangat mudah untuk dilewati.

---

## Kesimpulan

Lab ini secara efektif menunjukkan bahaya dari kebocoran informasi melalui kode *client-side*. Ini menjadi pengingat bahwa keamanan melalui pengelabuan (*security through obscurity*) bukanlah strategi yang efektif. Kontrol akses yang kuat harus selalu diterapkan di sisi server, yang bertindak sebagai satu-satunya sumber kebenaran untuk hak akses pengguna.

## Bukti
<img width="1920" height="1080" alt="Screenshot (596)" src="https://github.com/user-attachments/assets/9d0547f0-0d72-4eb8-b0af-10ba3d485c98" />
<img width="1920" height="1080" alt="Screenshot (597)" src="https://github.com/user-attachments/assets/10d6a4bf-4e51-4d5f-b37b-15e494578a17" />
<img width="1920" height="1080" alt="Screenshot (598)" src="https://github.com/user-attachments/assets/e80f96b3-ba30-4321-a271-8ce5fff928a9" />
