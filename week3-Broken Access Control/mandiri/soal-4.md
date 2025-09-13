# Lab Write-Up: User ID controlled by request parameter with password disclosure

- **Platform:** PortSwigger Web Security Academy
- **Tingkat Kesulitan:** Apprentice
- **Link Lab:** `https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure`

---

##  Ringkasan

Lab ini mendemonstrasikan kerentanan kritis yang timbul dari kombinasi dua kelemahan: **Insecure Direct Object Reference (IDOR)** dan **Sensitive Information Disclosure (Bocoran Informasi Sensitif)**. Aplikasi ini memungkinkan pengguna untuk melihat data akun pengguna lain dengan memanipulasi parameter `id` di URL. Secara fatal, data yang ditampilkan juga mencakup password pengguna dalam bentuk *plaintext* di dalam kode sumber HTML.

Tujuan dari lab ini adalah mengeksploitasi kerentanan ini untuk mencuri password administrator, kemudian menggunakannya untuk login dan menghapus pengguna `carlos`.

---

##  Analisis Kerentanan

Kerentanan ini merupakan eskalasi dari lab sebelumnya dan terdiri dari dua masalah utama:

1.  **IDOR (Insecure Direct Object Reference):** Aplikasi gagal melakukan pemeriksaan otorisasi yang memadai. Aplikasi hanya memvalidasi sesi login pengguna, tetapi tidak memeriksa apakah pengguna tersebut berhak melihat data yang diminta melalui parameter `id` di URL.
2.  **Password Disclosure:** Halaman "My Account" memiliki kolom password yang terisi otomatis. Meskipun di antarmuka pengguna (UI) ditampilkan sebagai input yang ditutupi (masked `••••••`), nilai *plaintext* dari password tersebut disematkan langsung di dalam atribut `value` pada tag `<input>` di kode HTML.

Dengan menggabungkan kedua kerentanan ini, seorang penyerang dapat meminta halaman akun milik `administrator` dan dengan mudah membaca password mereka dari kode sumber HTML.

---

##  Tools yang Digunakan

* Web Browser (dengan fitur "View Page Source" / "Inspect Element")

---

##  Langkah-langkah Penyelesaian (Walkthrough)

Serangan ini dilakukan dalam dua fase: mendapatkan kredensial dan menggunakannya.

### **Fase 1: Mendapatkan Password Administrator **

1.  **Login:** Saya masuk ke aplikasi menggunakan akun tingkat rendah yang disediakan: `wiener:peter`.

2.  **Eksploitasi IDOR:** Setelah login, saya membuka halaman "My Account" dan mengamati URL-nya: `.../my-account?id=wiener`. Saya kemudian memanipulasi URL ini, mengubah `wiener` menjadi `administrator`:
    ```
    https://<ID-LAB>.web-security-academy.net/my-account?id=administrator
    ```

3.  **Ekstraksi Password:** Setelah mengakses URL yang telah dimodifikasi, halaman akun administrator dimuat. Saya mengklik kanan dan memilih "View Page Source". Di dalam kode sumber, saya mencari kata `password` dan menemukan baris berikut:
    ```html
    <input required type="password" name="password" value="... StolenAdminPassword ...">
    ```
    Saya menyalin nilai dari atribut `value`, yang merupakan password *plaintext* milik administrator.

### **Fase 2: Eskalasi Hak Istimewa & Penyelesaian Lab **

1.  **Logout & Login Kembali:** Saya keluar dari akun `wiener`. Kemudian, saya kembali ke halaman login dan masuk menggunakan kredensial yang baru saja saya curi:
    * **Username:** `administrator`
    * **Password:** [Password yang disalin dari Fase 1]

2.  **Menghapus Pengguna:** Setelah berhasil login sebagai administrator, saya mendapatkan akses ke panel admin. Di panel tersebut, saya menemukan pengguna `carlos` dan mengklik tombol **Delete**.

3.  Tindakan ini berhasil menyelesaikan lab.

---

##  Rekomendasi Mitigasi

1.  **Terapkan Pemeriksaan Otorisasi yang Ketat:** Server harus selalu memverifikasi bahwa pengguna yang diautentikasi memiliki izin untuk mengakses atau memanipulasi data yang mereka minta. Jangan pernah mengandalkan parameter yang dikontrol pengguna (`id` di URL) sebagai satu-satunya penentu objek data.

2.  **Jangan Pernah Mengirimkan Data Sensitif ke Klien:** Password dalam bentuk *plaintext* (atau data sangat sensitif lainnya) tidak boleh dikirim ke sisi klien dalam keadaan apa pun, bahkan untuk "kenyamanan" mengisi formulir secara otomatis. Ini adalah pelanggaran keamanan yang fundamental. Kolom password seharusnya tidak pernah diisi otomatis dengan nilai yang sebenarnya.

---

##  Kesimpulan

Lab ini adalah ilustrasi yang kuat tentang bagaimana dua kerentanan dengan tingkat keparahan sedang dapat dirangkai (*chained*) menjadi satu serangan dengan dampak kritis. Sebuah kerentanan IDOR yang mungkin hanya bisa melihat informasi profil menjadi jauh lebih berbahaya ketika digabungkan dengan bocornya kredensial, yang pada akhirnya mengarah pada pengambilalihan akun administrator sepenuhnya.

## Bukti
<img width="1920" height="1080" alt="Screenshot (7)" src="https://github.com/user-attachments/assets/4e4b5dce-b075-42e0-90cf-8febdf636129" />
<img width="1920" height="1080" alt="Screenshot (8)" src="https://github.com/user-attachments/assets/8911fafc-64af-4764-88b3-185e36576dfe" />
<img width="1920" height="1080" alt="Screenshot (9)" src="https://github.com/user-attachments/assets/74b5fb2a-340c-45d6-aac5-fd2084141b4b" />
