# Write-Up Challenge: Admin Section (OWASP Juice Shop)

- **Platform:** OWASP Juice Shop
- **Kategori Tantangan:** Broken Access Control
- **Tingkat Kesulitan:** ⭐⭐ (2/6)

---

##  Ringkasan

Tantangan "Admin Section" menguji kemampuan untuk menemukan *endpoint* tersembunyi dan melewati kontrol akses sederhana. Halaman administrasi aplikasi tidak tertaut di mana pun dari antarmuka pengguna, tetapi *path* URL-nya dapat ditemukan dengan menganalisis file JavaScript sisi klien. Untuk berhasil mengaksesnya, pengguna harus terlebih dahulu mendapatkan hak akses sebagai administrator, dalam hal ini dengan login menggunakan kredensial default.

---

##  Analisis Kerentanan

Kerentanan utama adalah **Broken Access Control**, yang diperparah oleh **Sensitive Information Disclosure** di dalam kode sisi klien.

1.  **Bocoran Informasi di JavaScript:** Rute atau *path* aplikasi, termasuk yang sensitif seperti `/administration`, didefinisikan di dalam file JavaScript (`main.js`) yang dapat diakses dan dibaca oleh siapa saja. Ini memungkinkan penyerang untuk memetakan area tersembunyi dari aplikasi.
2.  **Kontrol Akses yang Lemah:** Meskipun halaman `/administration` memiliki mekanisme perlindungan (tidak seperti tantangan *sandbox*), perlindungan tersebut hanya memeriksa apakah pengguna yang login memiliki peran sebagai admin. Kelemahan ini dapat dieksploitasi jika akun admin memiliki kredensial yang lemah atau dapat ditebak (dalam kasus ini, kredensial default).

---

##  Tools yang Digunakan

* Web Browser
* Browser Developer Tools (F12)

---

##  Langkah-langkah Penyelesaian (Walkthrough)

Serangan dilakukan dalam dua fase: penemuan (*discovery*) dan eksploitasi (*exploitation*).

### **Fase 1: Menemukan Endpoint Tersembunyi**

1.  **Analisis Kode Klien:** Saya membuka Developer Tools (F12) di browser dan menavigasi ke tab **Sources** (atau **Debugger**).
2.  Saya menemukan dan membuka file JavaScript utama aplikasi (misalnya, `main.js` atau `main-es2018.js`).
3.  Dengan menggunakan fitur pencarian (Ctrl+F), saya mencari kata kunci seperti `admin` atau `path`.
4.  Pencarian ini mengungkap definisi rute untuk halaman administrasi:
    ```javascript
    // ...
    {
        path: "administration",
        component: AdministrationComponent,
        canActivate: [AdminGuard]
    }
    // ...
    ```
    Dari sini, saya mengetahui bahwa path yang benar adalah `/#/administration`.

### **Fase 2: Melewati Kontrol Akses**

1.  **Percobaan Gagal:** Percobaan pertama untuk mengakses `http://localhost:3000/#/administration` tanpa login atau sebagai pengguna biasa gagal dan menampilkan pesan error, karena `AdminGuard` berhasil memblokir akses.

2.  **Eskalasi Hak Istimewa:** Saya login ke aplikasi menggunakan kredensial admin default:
    * **Email:** `admin@juice-sh.op`
    * **Password:** `admin123`

3.  **Akses Berhasil:** Setelah login sebagai admin, saya kembali mengakses URL `http://localhost:3000/#/administration`. Kali ini, `AdminGuard` mengizinkan akses, dan halaman "Administration" berhasil dimuat.

4.  Tantangan pun langsung ditandai sebagai selesai.

---

##  Rekomendasi Mitigasi

1.  **Perkuat Keamanan Akun Admin:** Nonaktifkan atau ganti kredensial default segera setelah instalasi. Terapkan kebijakan password yang kuat dan Multi-Factor Authentication (MFA) untuk semua akun dengan hak istimewa.

2.  **Minimalkan Bocoran Informasi:** Meskipun sulit dihindari dalam *Single-Page Applications* (SPA), pertimbangkan untuk memuat definisi rute secara dinamis berdasarkan peran pengguna. Dengan begitu, pengguna biasa tidak akan melihat *path* admin di file JavaScript mereka.

3.  **Keamanan Berlapis (Defense-in-Depth):** Jangan hanya mengandalkan satu lapis perlindungan. Selain otorisasi berbasis peran, pertimbangkan untuk membatasi akses ke halaman admin berdasarkan alamat IP (IP whitelisting) atau menempatkannya di belakang jaringan privat (VPN).

---

##  Kesimpulan

Tantangan ini menunjukkan bahwa kontrol akses yang fungsional sekalipun bisa menjadi tidak berguna jika akun dengan hak istimewa dapat dengan mudah disusupi. Ini juga menekankan bahwa "menyembunyikan" sebuah URL (*security through obscurity*) bukanlah strategi keamanan yang efektif, karena penyerang yang gigih dapat menemukan *endpoint* tersembunyi dengan menganalisis kode sisi klien.

## Bukti
<img width="1920" height="1080" alt="Screenshot (15)" src="https://github.com/user-attachments/assets/f2a79448-2fae-4a4c-865a-05b5427b103d" />
<img width="1920" height="1080" alt="Screenshot (16)" src="https://github.com/user-attachments/assets/6861f93f-54c3-4576-aaff-337d5e48e7c6" />
<img width="1920" height="1080" alt="Screenshot (17)" src="https://github.com/user-attachments/assets/1be64636-da4d-4af1-af87-1afb4fc37165" />

