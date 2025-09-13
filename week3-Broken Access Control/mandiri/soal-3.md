# Lab Write-Up: User ID controlled by request parameter

- **Platform:** PortSwigger Web Security Academy
- **Tingkat Kesulitan:** Apprentice
- **Link Lab:** `https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter`

---

##  Ringkasan

Lab ini mendemonstrasikan kerentanan **Horizontal Privilege Escalation** yang disebabkan oleh **Insecure Direct Object Reference (IDOR)**. Aplikasi mengizinkan pengguna untuk melihat data akun pengguna lain hanya dengan memanipulasi parameter `id` pada URL. Tujuan dari lab ini adalah untuk mengeksploitasi kelemahan ini guna mendapatkan API key milik pengguna `carlos`.

---

##  Analisis Kerentanan

Aplikasi ini memiliki kelemahan fatal dalam mekanisme kontrol aksesnya. Saat seorang pengguna mengakses halaman akun (`/my-account`), aplikasi menggunakan parameter `id` dari URL untuk menentukan data pengguna mana yang harus diambil dari database dan ditampilkan.

Contoh URL: `/my-account?id=wiener`

Masalahnya adalah, server **hanya memverifikasi bahwa ada sesi login yang aktif**, tetapi **gagal memverifikasi apakah pengguna yang login (`wiener`) cocok dengan `id` yang diminta di URL**. Akibatnya, pengguna yang terautentikasi dapat mengganti nilai `id` di URL dengan nama pengguna lain (`carlos`) dan server akan dengan patuh menyajikan data pribadi milik pengguna tersebut.

---

##  Tools yang Digunakan

* Web Browser

---

##  Langkah-langkah Penyelesaian (Walkthrough)

Eksploitasi untuk kerentanan ini sangat sederhana dan dapat dilakukan langsung di browser.

1.  **Login:** Saya login ke aplikasi menggunakan kredensial yang disediakan: `wiener:peter`.

2.  **Observasi URL:** Setelah login, saya diarahkan ke halaman "My Account". Saya mengamati address bar browser dan melihat URL-nya adalah:
    ```
    https://<ID-LAB>.web-security-academy.net/my-account?id=wiener
    ```

3.  **Manipulasi Parameter:** Saya mengklik address bar dan mengubah nilai parameter `id` dari `wiener` menjadi `carlos`. URL baru menjadi:
    ```
    https://<ID-LAB>.web-security-academy.net/my-account?id=carlos
    ```

4.  **Ekstraksi Informasi:** Setelah menekan Enter, halaman dimuat ulang. Meskipun saya login sebagai `wiener`, halaman tersebut kini menampilkan semua informasi akun milik `carlos`, termasuk **API key** miliknya.

5.  **Menyelesaikan Lab:** Saya menyalin API key tersebut dan mengirimkannya melalui formulir "Submit solution" untuk menyelesaikan lab.

---

##  Rekomendasi Mitigasi

Kerentanan IDOR seperti ini harus ditangani di sisi server dengan menerapkan pemeriksaan otorisasi yang ketat.

1.  **Verifikasi Kepemilikan Objek:** Untuk setiap permintaan yang mengakses data sensitif, server harus memvalidasi bahwa pengguna yang diautentikasi (berdasarkan sesi atau token mereka) adalah pemilik sah dari data yang diminta. Dalam kasus ini, saat menerima permintaan untuk `/my-account?id=carlos`, server harus memeriksa: "Apakah ID pengguna dari sesi saat ini sama dengan 'carlos'?". Jika tidak, permintaan harus ditolak (misalnya, dengan respons `403 Forbidden`).

2.  **Hindari Penggunaan ID Langsung:** Jika memungkinkan, hindari penggunaan ID yang dapat ditebak (seperti username) di URL. Sebaiknya, aplikasi mengambil data pengguna berdasarkan ID yang disimpan di dalam sesi login mereka. Misalnya, endpoint `/my-account` (tanpa parameter `id`) seharusnya secara otomatis mengambil data untuk pengguna yang sedang login.

---

##  Kesimpulan

Lab ini adalah contoh sempurna dari kerentanan IDOR. Ini menyoroti betapa pentingnya untuk tidak pernah mempercayai input yang dapat dikontrol pengguna, terutama ketika input tersebut digunakan untuk mengakses sumber daya seperti data pengguna. Setiap permintaan harus melewati pemeriksaan otorisasi yang ketat di sisi server.

## Bukti
<img width="1920" height="1080" alt="Screenshot (599)" src="https://github.com/user-attachments/assets/5fdf5a92-8b25-4b24-b4a0-4dfe68d9ad08" />
<img width="1920" height="1080" alt="Screenshot (3)" src="https://github.com/user-attachments/assets/84baf754-97b6-4471-a61c-4f058a92c4e3" />
<img width="1920" height="1080" alt="Screenshot (4)" src="https://github.com/user-attachments/assets/d35f2c5d-fa8a-4d90-b19a-a78f595fd23b" />
<img width="1920" height="1080" alt="Screenshot (5)" src="https://github.com/user-attachments/assets/96fce2aa-11f4-4209-a9e7-5e7e5f4fc2a9" />
