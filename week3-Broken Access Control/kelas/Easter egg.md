# Write-Up Challenge: Find the Easter Egg (OWASP Juice Shop)

- **Platform:** OWASP Juice Shop
- **Kategori Tantangan:** Broken Access Control
- **Tingkat Kesulitan:** ⭐⭐⭐⭐ (4/6)

---

##  Ringkasan

Tantangan "Find the Easter Egg" melibatkan penemuan file tersembunyi di dalam direktori FTP yang terekspos. Tantangan utamanya adalah melewati mekanisme kontrol akses yang membatasi pengunduhan file hanya pada ekstensi tertentu (misalnya, `.pdf` dan `.md`). Kerentanan ini dieksploitasi menggunakan teknik klasik yaitu **Poison Null Byte Injection** untuk menipu filter keamanan dan mengunduh file yang seharusnya terlarang.

---

## 🔬 Analisis Kerentanan

Kerentanan inti pada tantangan ini adalah **Broken Access Control** pada fungsionalitas unduh file, yang dieksploitasi melalui **Poison Null Byte Injection**.

1.  **Direktori FTP yang Terekspos:** Aplikasi secara tidak aman membuka akses ke direktori `/ftp`, memungkinkan siapa saja untuk melihat daftar filenya.
2.  **Filter Ekstensi yang Lemah:** Server menerapkan filter di sisi *back-end* untuk hanya mengizinkan pengunduhan file dengan ekstensi yang ada di daftar putih (e.g., `.pdf`).
3.  **Poison Null Byte (`%00`):** Karakter *null byte* diinterpretasikan sebagai akhir dari sebuah *string* oleh banyak bahasa pemrograman dan sistem tingkat rendah (seperti C/C++). Dengan menyisipkan *null byte* yang sudah di-URL-encode (`%00`) di tengah nama file, kita bisa membuat sistem keamanan dan sistem file menginterpretasikan nama file yang berbeda. Untuk melewati filter tambahan dari server web, seringkali digunakan *double URL encoding* menjadi `%2500`.

Saat server menerima permintaan untuk file `eastere.gg%2500.pdf`, logika keamanannya mungkin membaca string lengkap dan menyetujuinya karena berakhiran `.pdf`. Namun, saat diserahkan ke sistem file, pembacaan string berhenti di karakter *null byte*, sehingga file yang sebenarnya diakses adalah `eastere.gg`.

---

##  Tools yang Digunakan

* Web Browser

---

##  Langkah-langkah Penyelesaian (Walkthrough)

1.  **Penemuan Direktori:** Saya menavigasi ke `http://localhost:3000/ftp`. Di sana saya menemukan daftar file, termasuk file target bernama **`eastere.gg`**.

2.  **Verifikasi Filter:** Saya mencoba mengklik langsung `eastere.gg` untuk mengunduhnya. Sesuai dugaan, server menolak permintaan tersebut karena file tidak memiliki ekstensi yang diizinkan.

3.  **Membuat Payload Eksploitasi:** Saya membuat URL yang telah dimanipulasi dengan menyisipkan *double-encoded null byte* (`%2500`) di antara nama file target dan ekstensi yang diizinkan.

4.  **Eksploitasi:** Saya memasukkan URL berikut langsung ke *address bar* browser dan menekan Enter:
    ```
    http://localhost:3000/ftp/eastere.gg%2500.pdf
    ```

5.  **Hasil:** Server berhasil ditipu. Pemeriksaan keamanan menyetujui permintaan karena melihat adanya `.pdf`, tetapi sistem file hanya memproses `eastere.gg`. Akibatnya, file `eastere.gg` berhasil diunduh, dan tantangan pun selesai.

6.  **Membuka Easter Egg:** Saya membuka file `eastere.gg` yang diunduh dengan editor teks. File tersebut berisi string Base64 yang, setelah didekode, mengungkapkan pesan tersembunyi.

---

##  Rekomendasi Mitigasi

1.  **Sanitasi Input Pengguna:** Validasi dan sanitasi semua input dari pengguna di sisi server. Secara spesifik, hapus atau tolak karakter kontrol seperti *null byte*.
2.  **Lakukan Validasi Setelah Normalisasi Path:** Sebelum melakukan pemeriksaan keamanan (seperti memeriksa ekstensi), normalisasikan path file ke bentuk kanoniknya. Proses ini akan menyelesaikan *null byte* sebelum pemeriksaan dilakukan, sehingga tipuan tidak akan berhasil.
3.  **Verifikasi Tipe File Berdasarkan Konten:** Jangan hanya mengandalkan ekstensi file yang diberikan pengguna. Verifikasi tipe file sebenarnya dengan memeriksa *MIME type* atau *magic bytes* dari konten file tersebut.

---

##  Kesimpulan

Tantangan ini adalah demonstrasi yang sangat baik tentang bagaimana kerentanan klasik seperti *Poison Null Byte Injection* dapat digunakan untuk melewati kontrol akses yang tampaknya aman. Ini menggarisbawahi aturan fundamental dalam keamanan aplikasi: **jangan pernah mempercayai input pengguna** dan selalu lakukan validasi berlapis di sisi server.

## Bukti
<img width="1920" height="1080" alt="Screenshot (22)" src="https://github.com/user-attachments/assets/d77cd4d5-0152-4b79-aa01-75784e6cb382" />
<img width="1920" height="1080" alt="Screenshot (23)" src="https://github.com/user-attachments/assets/4dfba251-0bf8-4c6b-ae63-9f3f2f100316" />
<img width="1920" height="1080" alt="Screenshot (24)" src="https://github.com/user-attachments/assets/307a0df8-fd2f-4738-b567-7a8310e2ca45" />
<img width="1920" height="1080" alt="Screenshot (25)" src="https://github.com/user-attachments/assets/bec0339e-23cc-4234-8413-d264bebabb07" />
<img width="1920" height="1080" alt="Screenshot (26)" src="https://github.com/user-attachments/assets/fbef6902-0a9d-4e99-9432-e34c6737ef5c" />
