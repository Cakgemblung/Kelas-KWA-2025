# Write-Up Challenge: Forged Feedback (OWASP Juice Shop)

- **Platform:** OWASP Juice Shop
- **Kategori Tantangan:** Broken Access Control
- **Tingkat Kesulitan:** ⭐⭐⭐ (3/6)

---

##  Ringkasan

Tantangan "Forged Feedback" mengeksploitasi kerentanan **Insecure Direct Object Reference (IDOR)** pada fungsi pengiriman *feedback*. Penyerang dapat mengirimkan *feedback* atas nama pengguna lain—termasuk administrator—dengan memanipulasi parameter `UserId` yang dikirim dalam *body* dari sebuah `POST` request. Ini menunjukkan kegagalan server dalam memvalidasi kepemilikan data yang dikirim oleh klien.

---

##  Analisis Kerentanan

Kerentanan utamanya adalah **Broken Access Control**. Saat seorang pengguna mengirimkan *feedback*, aplikasi mengirimkan sebuah `POST` request ke *endpoint* `/api/Feedbacks/`. Di dalam *body* request tersebut, terdapat sebuah *field* JSON bernama `UserId` yang berisi ID dari pengguna yang sedang login.

Masalahnya adalah, server secara buta **mempercayai nilai `UserId` yang dikirim oleh klien**. Server tidak melakukan validasi silang untuk memastikan bahwa `UserId` yang ada di dalam *body* request sama dengan `UserId` yang terkait dengan sesi login (yang tersimpan di dalam *session cookie* atau token). Celah ini memungkinkan penyerang untuk mengubah nilai `UserId` menjadi ID pengguna lain dan secara efektif mengirimkan data atas nama mereka.

---

##  Tools yang Digunakan

* **Burp Suite** atau **OWASP ZAP**

---

##  Langkah-langkah Penyelesaian (Walkthrough)

1.  **Login dan Persiapan:** Saya login ke aplikasi menggunakan akun pengguna biasa untuk mendapatkan sesi yang valid.

2.  **Memicu Request:** Saya menavigasi ke halaman **"Customer Feedback"** dan menyiapkan *proxy* (Burp/ZAP) untuk mencegat traffic (`Intercept is on`). Saya kemudian mengisi dan mengirimkan formulir *feedback* secara normal.

3.  **Mencegat dan Menganalisis:** *Proxy* berhasil menangkap sebuah `POST` request yang ditujukan ke `/api/Feedbacks/`. Saya memeriksa bagian *body* dari *request* tersebut dan menemukan struktur JSON sebagai berikut:
    ```json
    {"comment":"Feedback normal","rating":5,"UserId":12}
    ```
    Di sini, `12` adalah ID pengguna saya.

4.  **Memanipulasi `UserId`:** Saya memodifikasi nilai dari *key* `UserId`. Untuk menargetkan admin, saya mengubah nilainya menjadi `1`, yang merupakan ID umum untuk akun administrator di Juice Shop.
    ```json
    {"comment":"Feedback normal","rating":5,"UserId":1}
    ```

5.  **Mengirim Request yang Dimodifikasi:** Saya meneruskan (*forward*) *request* yang telah dimodifikasi. Server menerima *request* ini, dan karena tidak ada validasi yang tepat, server memprosesnya seolah-olah *feedback* tersebut dikirim oleh pengguna dengan ID 1 (admin). Seketika itu juga, tantangan ditandai sebagai selesai.

---

##  Rekomendasi Mitigasi

1.  **Abaikan `UserId` dari Klien:** Perbaikan yang paling penting adalah mengubah logika di *backend*. Server harus **mengabaikan sepenuhnya** nilai `UserId` yang dikirim dalam *body* request. Sebagai gantinya, server harus **hanya** menggunakan ID pengguna yang diambil dari sesi login yang terpercaya (*session cookie* atau token JWT) untuk menentukan siapa yang mengirimkan *feedback*.

2.  **Terapkan Token Anti-CSRF:** Untuk melindungi *endpoint* yang mengubah data seperti ini, gunakan token Anti-CSRF yang unik untuk setiap sesi. Ini akan mencegah jenis serangan lain yang dapat mengeksploitasi fungsionalitas ini.

---

## Kesimpulan

Tantangan ini adalah contoh sempurna dari kerentanan IDOR pada aksi tulis (*write action*), bukan hanya baca (*read action*). Ini membuktikan prinsip fundamental keamanan: **jangan pernah mempercayai input dari pengguna**, terutama ketika input tersebut digunakan untuk menentukan identitas atau otorisasi. Selalu andalkan sumber kebenaran yang dikelola server, seperti data sesi, untuk semua keputusan keamanan.

## Bukti 
<img width="1920" height="1080" alt="Screenshot (34)" src="https://github.com/user-attachments/assets/0dec72c4-f101-4715-8fa6-c6ac5abdd101" />

<img width="1920" height="1080" alt="Screenshot (35)" src="https://github.com/user-attachments/assets/b054d95c-c876-4e0d-bdc2-28f4451b7884" />

<img width="1920" height="1080" alt="Screenshot (36)" src="https://github.com/user-attachments/assets/4fa80166-61a5-4e2f-a73b-32b1c45a117e" />

<img width="1920" height="1080" alt="Screenshot (37)" src="https://github.com/user-attachments/assets/993e39aa-9ba4-4224-81fc-12c2a4649dc8" />
