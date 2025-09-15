# Juice-Shop Write-up: Access Another User's Basket

## Ringkasan Tantangan

**Judul:** Access another user's basket

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐ (2/6)

Tantangan ini menguji kemampuan untuk mengakses sumber daya milik pengguna lain—dalam hal ini, keranjang belanja—dengan memanipulasi referensi objek langsung yang tidak aman. Ini adalah contoh klasik dari kerentanan **Insecure Direct Object Reference (IDOR)**.

## Alat yang Digunakan

*   **Burp Suite:** Digunakan untuk memantau riwayat permintaan HTTP dan memodifikasi serta mengirim ulang permintaan menggunakan fitur Repeater.

## Metodologi dan Solusi

Solusi untuk tantangan ini adalah dengan mengidentifikasi bagaimana aplikasi mengambil data keranjang belanja dan kemudian memanipulasi parameter ID dalam permintaan untuk mengakses keranjang milik pengguna lain.

1.  **Menganalisis Permintaan untuk Mengakses Keranjang:**
    *   Setelah login, akses halaman keranjang belanja Anda sendiri (`Your Basket`).
    *   Buka tab **Proxy > HTTP history** di Burp Suite untuk memeriksa lalu lintas jaringan.
    *   Identifikasi permintaan `GET` yang ditujukan ke endpoint API untuk mengambil data keranjang. Endpoint ini biasanya memiliki pola seperti `/rest/basket/{basketId}`. Dalam kasus ini, permintaan yang ditemukan adalah `GET /rest/basket/1`, di mana `1` adalah ID keranjang milik pengguna yang sedang login.

2.  **Mengirim Permintaan ke Repeater:**
    *   Klik kanan pada permintaan `GET /rest/basket/1` di HTTP history.
    *   Pilih **"Send to Repeater"** untuk memindahkan permintaan ke tab Repeater agar dapat dimodifikasi dengan mudah.

3.  **Memanipulasi ID Keranjang (IDOR):**
    *   Di tab **Repeater**, baris pertama dari permintaan menunjukkan `GET /rest/basket/1 HTTP/1.1`.
    *   Ubah ID keranjang dari `1` menjadi angka lain yang berpotensi menjadi ID keranjang pengguna lain, misalnya `2`. Permintaan diubah menjadi:
        ```http
        GET /rest/basket/2 HTTP/1.1
        ```
    *   Klik tombol **"Send"** untuk mengirimkan permintaan yang telah dimodifikasi.

4.  **Verifikasi:**
    *   Server merespons dengan kode status `200 OK` dan mengembalikan data JSON yang berisi item-item di dalam keranjang dengan ID `2`.
    *   Ini membuktikan bahwa tidak ada pemeriksaan otorisasi yang memadai di sisi server.
    *   Secara bersamaan, tantangan ini ditandai sebagai `"Solved"` di antarmuka Juice Shop.

### Penjelasan Solusi

Kerentanan IDOR ini terjadi karena aplikasi hanya mengandalkan ID yang diberikan dalam URL (`/rest/basket/{basketId}`) untuk mengambil data tanpa melakukan validasi apakah pengguna yang diautentikasi saat ini memiliki izin untuk mengakses objek (keranjang) dengan ID tersebut. Dengan hanya mengubah ID di URL, seorang penyerang dapat melihat data sensitif milik pengguna lain.

## Saran Perbaikan

Untuk menambal kerentanan semacam ini, pengembang harus menerapkan kontrol akses yang ketat di sisi server.

1.  **Pemeriksaan Kepemilikan (Ownership Check):** Sebelum mengambil dan menampilkan data sumber daya apa pun, server harus selalu memverifikasi bahwa ID pengguna yang tersimpan di sesi cocok dengan ID pemilik sumber daya yang diminta.
2.  **Hindari Referensi Objek Langsung yang Dapat Ditebak:** Jika memungkinkan, gunakan pengidentifikasi yang acak dan sulit ditebak (seperti UUID) sebagai pengganti ID numerik yang berurutan (1, 2, 3, ...).
3.  **Prinsip Hak Istimewa Terendah (Principle of Least Privilege):** Pastikan pengguna hanya memiliki akses ke data dan fungsionalitas yang benar-benar mereka butuhkan.

## Bukti
<img width="1920" height="1080" alt="Screenshot (46)" src="https://github.com/user-attachments/assets/3bba10c7-14d6-4797-b730-e2b9d1df5e57" />

