# Write-Up: Manipulate Basket (OWASP Juice Shop)

### **Ringkasan Tantangan**

* **Nama:** Manipulate Basket
* **Kategori:** Broken Access Control
* **Tingkat Kesulitan:** ⭐⭐⭐ (Menengah)

Tantangan ini mengharuskan kita untuk menambahkan sebuah item ke dalam keranjang belanja milik pengguna lain. Ini menguji kemampuan kita untuk menemukan dan mengeksploitasi celah keamanan **IDOR (Insecure Direct Object References)** yang diperparah oleh **HTTP Parameter Pollution (HPP)**.

### **Alat yang Digunakan**

* **Web Browser** (Chrome, Firefox, dll.)
* **Burp Suite** (Untuk mencegat, menganalisis, dan memanipulasi request HTTP)

---

### **Langkah-langkah Penyelesaian**

#### **1. Observasi dan Analisis Fungsi Keranjang**
Langkah pertama adalah memahami bagaimana aplikasi menangani operasi keranjang belanja.
1.  Login ke akun Juice Shop.
2.  Matikan **Intercept** di Burp Suite, lalu bersihkan **HTTP History** (di tab Proxy) untuk memulai dari awal.
3.  Di aplikasi Juice Shop, tambahkan satu item ke keranjang.
4.  Buka tab **HTTP History** di Burp Suite. Cari request terbaru dengan method **`POST`** yang ditujukan ke endpoint `/api/BasketItems/`.
5.  Analisis request ini. Di bagian *Request body*, kita bisa melihat data dikirim dalam format JSON. Dari respons server, kita bisa mengonfirmasi **`BasketId`** milik kita.
    * **`BasketId` ditemukan:** `1`

#### **2. Merancang Serangan**
Karena `BasketId` dikirim dari sisi klien, ada potensi untuk memanipulasinya. Rencananya adalah mencoba menambahkan item ke keranjang lain. Kita akan menargetkan `BasketId` yang berdekatan dengan milik kita, misalnya `2`.

#### **3. Eksploitasi dengan HTTP Parameter Pollution (HPP)**
Di sinilah trik utamanya. Kita akan mengirimkan parameter `BasketId` sebanyak dua kali untuk membingungkan logika di backend.
1.  Klik kanan pada request `POST /api/BasketItems/` di HTTP History, lalu pilih **Send to Repeater**.
2.  Pindah ke tab **Repeater**.
3.  Modifikasi *body* JSON pada panel Request. Tambahkan parameter `BasketId` kedua dengan ID target kita.

    **Body Asli:**
    ```json
    {"ProductId":41,"BasketId":"1","quantity":1}
    ```

    **Body yang Dimodifikasi:**
    ```json
    {"ProductId":41,"BasketId":"1","quantity":1,"BasketId":"2"}
    ```
4.  Klik tombol **Send**.

#### **4. Verifikasi**
Setelah request yang dimanipulasi dikirim, server memberikan respons **`200 OK`**, menandakan operasi berhasil. Sesaat kemudian, notifikasi penyelesaian tantangan "Manipulate Basket" muncul di aplikasi Juice Shop.

---

### **Penjelasan Celah Keamanan**

Celah keamanan ini terjadi karena adanya **inkonsistensi dalam pemrosesan parameter duplikat** di sisi server.
* **Pemeriksaan Keamanan:** Bagian kode yang memvalidasi otorisasi kemungkinan hanya membaca parameter `BasketId` yang **pertama** (`"1"`). Karena ID ini cocok dengan session pengguna, pemeriksaan pun lolos.
* **Eksekusi Logika Bisnis:** Bagian kode yang menjalankan operasi ke database kemungkinan membaca parameter `BasketId` yang **terakhir** (`"2"`). Karena pemeriksaan sudah lolos, kode ini langsung menambahkan item ke keranjang milik korban tanpa verifikasi ulang.

### **Rekomendasi Perbaikan**

1.  **Penanganan Parameter yang Konsisten:** Pastikan seluruh bagian aplikasi membaca parameter dengan cara yang sama (misalnya, selalu yang pertama atau selalu yang terakhir). Idealnya, konfigurasikan server untuk menolak request yang mengandung duplikat parameter kunci.
2.  **Validasi Kepemilikan di Sisi Server:** Setiap kali ada operasi yang melibatkan data milik pengguna (seperti keranjang belanja), server harus selalu memverifikasi bahwa objek yang dimanipulasi (misal: `BasketId`) adalah benar-benar milik pengguna yang sedang melakukan request.

### **Bukti**
<img width="1920" height="1080" alt="Screenshot (53)" src="https://github.com/user-attachments/assets/9270b7a4-c6be-4b9a-9fe6-e09bf60301ff" />

<img width="1920" height="1080" alt="Screenshot (51)" src="https://github.com/user-attachments/assets/8118870b-d6ed-4838-8059-32f1d2f312d5" />

<img width="1920" height="1080" alt="Screenshot (52)" src="https://github.com/user-attachments/assets/fe34b90f-fde6-4d9e-8785-2b37caca94f8" />
