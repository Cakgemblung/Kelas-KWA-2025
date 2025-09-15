# Write-Up Challenge: Forged Review (OWASP Juice Shop)

- **Platform:** OWASP Juice Shop
- **Kategori Tantangan:** Broken Access Control
- **Tingkat Kesulitan:** ⭐⭐⭐ (3/6)

---

## Ringkasan

Tantangan "Forged Review" adalah sebuah eksploitasi klasik dari kerentanan **Insecure Direct Object Reference (IDOR)** pada fungsi tulis (*write action*). Tantangan ini mengharuskan peserta untuk mengirimkan sebuah ulasan produk, namun memalsukannya seolah-olah ulasan tersebut dikirim oleh pengguna lain. Ini berhasil dilakukan dengan memanipulasi parameter `author` dalam sebuah *request* yang dicegat, menunjukkan kelemahan server dalam memvalidasi identitas pengirim data.

---

## Analisis Kerentanan

Kerentanan utamanya adalah **Broken Access Control**. Saat seorang pengguna mengirimkan ulasan produk, aplikasi mengirimkan *request* ke *endpoint* API yang relevan. Di dalam *body* dari *request* tersebut, terdapat sebuah *field* JSON bernama `author` yang berisi alamat email dari pengguna yang sedang login.

Celah keamanannya adalah server secara buta **mempercayai nilai `author` yang dikirim oleh klien**. Server tidak melakukan validasi untuk memastikan bahwa email di *field* `author` sama dengan email yang terkait dengan sesi login pengguna saat ini. Hal ini memungkinkan seorang penyerang untuk mencegat *request* dan mengubah nilai `author` menjadi email pengguna lain, sehingga berhasil memalsukan ulasan atas nama mereka.

---

## Tools yang Digunakan

* **Burp Suite** atau **OWASP ZAP**

---

## Langkah-langkah Penyelesaian (Walkthrough)

1.  **Login dan Persiapan:** Saya login ke aplikasi menggunakan akun yang valid untuk mendapatkan akses ke fitur penulisan ulasan.

2.  **Memicu Request:** Saya menavigasi ke halaman detail salah satu produk, kemudian menyiapkan *proxy* (Burp/ZAP) untuk mencegat traffic (`Intercept is on`). Saya menulis ulasan singkat di formulir yang tersedia dan mengklik "Submit".

3.  **Mencegat dan Menganalisis:** *Proxy* berhasil menangkap sebuah *request* yang ditujukan ke *endpoint* API untuk ulasan. Saya memeriksa bagian *body* dari *request* tersebut dan menemukan struktur JSON sebagai berikut:
    ```json
    {"message":"Ini ulasan yang dipalsukan!","author":"user-saya@juice-sh.op"}
    ```
    Di sini, `user-saya@juice-sh.op` adalah email akun yang saya gunakan.

4.  **Memanipulasi `author`:** Saya memodifikasi nilai dari *key* `author`. Untuk menyelesaikan tantangan, saya mengubahnya menjadi email pengguna lain yang ada di sistem, misalnya email admin:
    ```json
    {"message":"Ini ulasan yang dipalsukan!","author":"admin@juice-sh.op"}
    ```

5.  **Mengirim Request yang Dimodifikasi:** Saya meneruskan (*forward*) *request* yang telah dimodifikasi. Server menerima *request* ini dan, tanpa validasi yang tepat, mempublikasikan ulasan tersebut seolah-olah berasal dari akun admin. Seketika itu juga, tantangan ditandai sebagai selesai.

---

## Rekomendasi Mitigasi

1.  **Gunakan Identitas dari Sesi:** Perbaikan yang paling krusial adalah mengubah logika *backend*. Server harus **mengabaikan sepenuhnya** nilai `author` yang dikirim dalam *body* request. Sebagai gantinya, server harus **hanya** menggunakan identitas pengguna (email atau ID) yang diambil dari sesi login yang terpercaya (*session cookie* atau token) untuk menentukan siapa penulis ulasan.

2.  **Terapkan Token Anti-CSRF:** Melindungi *endpoint* yang mengubah data seperti ini dengan token Anti-CSRF akan menambahkan lapisan keamanan untuk mencegah serangan dari domain lain.

---

## Kesimpulan

Serupa dengan "Forged Feedback", tantangan ini kembali menegaskan betapa berbahayanya mempercayai data yang dikontrol oleh pengguna, terutama data yang berkaitan dengan identitas. Validasi di sisi server adalah kunci, di mana semua keputusan otorisasi harus didasarkan pada sumber data yang tidak dapat dimanipulasi oleh klien, seperti data sesi yang aman.

## Bukti 
<img width="1920" height="1080" alt="Screenshot (38)" src="https://github.com/user-attachments/assets/2b1d2a90-9cae-4757-8dda-2babb9ace6ad" />

<img width="1920" height="1080" alt="Screenshot (39)" src="https://github.com/user-attachments/assets/78ca4312-1a5a-42d8-b4a4-fbdff4a11cb2" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e30bbceb-0ccd-4824-9d36-acf4d8a0b5c9" />
