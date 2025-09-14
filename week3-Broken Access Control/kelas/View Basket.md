# Write-Up Challenge: View Basket (OWASP Juice Shop)

- **Platform:** OWASP Juice Shop
- **Kategori Tantangan:** Broken Access Control
- **Tingkat Kesulitan:** ⭐⭐ (2/6)

---

## 📝 Ringkasan

Tantangan "View Basket" mengeksploitasi kerentanan **Insecure Direct Object Reference (IDOR)** pada sebuah *endpoint* API. Aplikasi ini memungkinkan pengguna untuk melihat isi keranjang belanja pengguna lain dengan cara memanipulasi ID keranjang yang dikirimkan dalam sebuah *request*. Tantangan ini menunjukkan kegagalan aplikasi dalam melakukan validasi otorisasi di sisi server.

---

## 🔬 Analisis Kerentanan

Kerentanan utama adalah **Broken Access Control**. Saat pengguna melihat keranjang belanjanya, aplikasi membuat sebuah *request* ke API *backend*, misalnya `GET /rest/basket/5`, di mana `5` adalah ID unik untuk keranjang belanja pengguna tersebut.

Masalahnya adalah, *backend* aplikasi **hanya memeriksa apakah pengguna sudah login**, tetapi **gagal memeriksa apakah pengguna yang login tersebut adalah pemilik sah dari keranjang dengan ID 5**. Server secara buta mempercayai ID yang dikirimkan oleh klien. Akibatnya, seorang penyerang dapat dengan mudah mengubah ID di *request* tersebut (misalnya, menjadi `4`) untuk melihat isi keranjang milik pengguna lain.

---

## 🛠️ Tools yang Digunakan

* **OWASP ZAP** atau **Burp Suite** (untuk memodifikasi *request*)

---

## 🚀 Langkah-langkah Penyelesaian (Walkthrough)

1.  **Memicu Request:** Pertama, saya login ke aplikasi dan menambahkan satu barang ke keranjang belanja. Kemudian, saya mengklik ikon keranjang untuk melihat isinya. Aksi ini bertujuan untuk menghasilkan *request* yang sah ke API keranjang belanja.

2.  **Menemukan Request di History:** Saya membuka *tool proxy* (Burp/ZAP) dan melihat tab **HTTP History**. Saya menemukan *request* yang baru saja dibuat, yang polanya adalah:
    ```
    GET /rest/basket/5 HTTP/1.1
    ```
    Angka `5` adalah ID keranjang belanja akun saya.

3.  **Mengirim Request ke Repeater:** Saya mengklik kanan pada *request* tersebut dan memilih **"Send to Repeater"** (di Burp) atau **"Open / Resend with Request Editor"** (di ZAP) untuk mempermudah modifikasi.

4.  **Memanipulasi ID Objek:** Di dalam *Repeater/Request Editor*, saya mengubah ID objek pada baris pertama *request*. Saya mengubah ID dari `5` menjadi `4`:
    ```
    GET /rest/basket/4 HTTP/1.1
    ```

5.  **Mengirim Request yang Dimodifikasi:** Saya mengklik tombol **"Send"**. Server, yang tidak memiliki validasi kepemilikan, merespons dengan data keranjang belanja milik pengguna dengan ID 4. Saat *request* ini berhasil, tantangan langsung ditandai sebagai selesai.

---

## 🛡️ Rekomendasi Mitigasi

1.  **Terapkan Pemeriksaan Otorisasi di Sisi Server:** Ini adalah perbaikan yang paling penting. Setiap kali server menerima permintaan untuk mengakses sebuah sumber daya (seperti keranjang belanja), server **wajib** memverifikasi bahwa ID pengguna yang tersimpan di sesi (*session cookie* atau token) cocok dengan pemilik dari sumber daya yang diminta. Jika tidak cocok, permintaan harus ditolak (misalnya dengan status `403 Forbidden`).

2.  **Gunakan Pengidentifikasi yang Acak (Defense-in-Depth):** Hindari penggunaan ID yang berurutan dan mudah ditebak (1, 2, 3, ...). Sebaiknya gunakan pengidentifikasi yang acak dan panjang (seperti UUID) untuk setiap objek. Ini membuat penyerang tidak mungkin bisa menebak ID milik pengguna lain.

---

## ✅ Kesimpulan

Tantangan ini adalah demonstrasi klasik dari kerentanan IDOR. Ini mengajarkan pelajaran penting bahwa *endpoint* API yang mengakses data spesifik pengguna tidak boleh hanya bergantung pada autentikasi (memastikan pengguna sudah login), tetapi juga harus menerapkan otorisasi (memastikan pengguna yang login hanya bisa mengakses datanya sendiri).

## Bukti
<img width="1920" height="1080" alt="Screenshot (31)" src="https://github.com/user-attachments/assets/b0a25f1d-38db-493f-9f0f-2e40ee6e1fc2" />

<img width="1920" height="1080" alt="Screenshot (32)" src="https://github.com/user-attachments/assets/ab0fcd30-0139-4786-9d3a-e1dafc090c86" />

<img width="1920" height="1080" alt="Screenshot (33)" src="https://github.com/user-attachments/assets/5a7268d1-d253-4064-8756-fe962e06e9c2" />
