# Lab Write-Up: Insecure direct object references

- **Platform:** PortSwigger Web Security Academy
- **Tingkat Kesulitan:** Apprentice
- **Link Lab:** `https://portswigger.net/web-security/access-control/insecure-direct-object-references`

---

##  Ringkasan

Lab ini mendemonstrasikan kerentanan **Insecure Direct Object Reference (IDOR)** yang klasik pada fungsionalitas pengambilan file. Aplikasi menyimpan transkrip chat pengguna sebagai file statis (`.txt`) di server dan menggunakan URL yang dapat diprediksi untuk mengaksesnya. Dengan memanipulasi URL tersebut, dimungkinkan untuk mengakses file transkrip milik pengguna lain dan menemukan informasi sensitif. Tujuan lab ini adalah menemukan password milik `carlos` dan menggunakannya untuk login.

---

##  Analisis Kerentanan

Kerentanan inti terletak pada kegagalan aplikasi untuk menerapkan kontrol otorisasi saat pengguna meminta sebuah file transkrip. Mekanismenya adalah sebagai berikut:

1.  **Referensi Objek Langsung:** Aplikasi mereferensikan file transkrip secara langsung melalui URL, contohnya `/download-transcript/2.txt`.
2.  **Nama File yang Dapat Ditebak:** Nama file menggunakan pola numerik yang berurutan (`1.txt`, `2.txt`, dst.), sehingga sangat mudah bagi penyerang untuk menebak nama file lain.
3.  **Tidak Adanya Pemeriksaan Otorisasi:** Server tidak memeriksa apakah pengguna yang meminta file (misalnya `2.txt`) adalah pemilik sah dari chat tersebut. Server hanya akan menyajikan file apa pun yang diminta selama file itu ada.

Kombinasi dari ketiga faktor ini memungkinkan setiap pengguna untuk mengunduh dan membaca transkrip chat milik pengguna lain hanya dengan mengubah angka di URL.

---

##  Tools yang Digunakan

* Web Browser

---

##  Langkah-langkah Penyelesaian (Walkthrough)

1.  **Memahami Pola URL:** Saya pertama-tama menggunakan fitur **"Live chat"** dan mengirim pesan acak. Setelah itu, saya mengklik **"View transcript"** untuk mengunduh transkrip chat saya sendiri.

2.  **Observasi URL:** Saya mengamati URL dari file transkrip yang saya terima. Polanya adalah:
    ```
    https://<ID-LAB>.web-security-academy.net/download-transcript/2.txt
    ```
    Ini mengonfirmasi bahwa file diakses melalui ID numerik yang bisa ditebak.

3.  **Eksploitasi IDOR:** Saya memodifikasi URL di address bar browser, mengubah nama file dari `2.txt` menjadi `1.txt` untuk mencoba mengakses transkrip chat paling awal.
    ```
    https://<ID-LAB>.web-security-academy.net/download-transcript/1.txt
    ```

4.  **Ekstraksi Informasi:** Setelah menekan Enter, browser menampilkan isi file `1.txt`. Di dalam transkrip chat tersebut, saya menemukan kredensial login untuk pengguna `carlos` yang disebutkan secara eksplisit.

5.  **Penyelesaian Lab:** Saya kembali ke halaman utama lab, menavigasi ke halaman login, dan menggunakan password yang saya temukan untuk masuk sebagai `carlos`. Setelah login berhasil, lab terselesaikan.

---

##  Rekomendasi Mitigasi

1.  **Terapkan Kontrol Akses di Sisi Server:** Ini adalah mitigasi yang paling krusial. Sebelum menyajikan sebuah file, server harus memverifikasi bahwa pengguna yang sedang login (berdasarkan sesi mereka) adalah pemilik sah dari file transkrip yang diminta. Jika tidak, akses harus ditolak.

2.  **Gunakan Nama File yang Tidak Dapat Ditebak (Defense-in-Depth):** Sebagai lapisan pertahanan tambahan, aplikasi seharusnya menggunakan pengidentifikasi yang acak dan panjang (seperti UUID) sebagai nama file, contohnya `download-transcript/a1b2c3d4-e5f6-4a5b-8c9d-0e1f2a3b4c5d.txt`. Ini membuat penyerang tidak mungkin dapat menebak nama file milik pengguna lain.

---

##  Kesimpulan

Lab ini adalah contoh sempurna dari kerentanan IDOR berbasis file. Ini menunjukkan bahwa sumber daya (objek) yang direferensikan melalui nama yang dapat ditebak harus selalu dilindungi dengan pemeriksaan otorisasi yang ketat di sisi server untuk memastikan hanya pengguna yang sah yang dapat mengaksesnya.

## Bukti
<img width="1920" height="1080" alt="Screenshot (10)" src="https://github.com/user-attachments/assets/a33c6e28-a50f-48af-ac7c-21afac8adcd5" />
<img width="1920" height="1080" alt="Screenshot (11)" src="https://github.com/user-attachments/assets/39785677-f701-4970-a327-556896f084ad" />
<img width="1920" height="1080" alt="Screenshot (12)" src="https://github.com/user-attachments/assets/835ab5e0-3c10-492a-abc3-c2cd64ef727e" />
<img width="1920" height="1080" alt="Screenshot (13)" src="https://github.com/user-attachments/assets/c8f193be-d97f-48b2-ba26-26e7799e4f22" />
