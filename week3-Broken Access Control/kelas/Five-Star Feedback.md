# Write-Up Challenge: Five-Star Feedback (OWASP Juice Shop)

- **Platform:** OWASP Juice Shop
- **Kategori Tantangan:** Broken Access Control
- **Tingkat Kesulitan:** ⭐⭐ (2/6)

---

##  Ringkasan

Tantangan "Five-Star Feedback" berfokus pada penyalahgunaan fungsionalitas yang ada (**Abuse of Functionality**) setelah mendapatkan hak akses sebagai administrator. Tujuannya adalah untuk memanipulasi data aplikasi dengan cara menghapus semua ulasan pengguna yang memiliki rating bintang lima. Tantangan ini menyoroti bahwa kontrol akses tidak hanya tentang siapa yang bisa masuk, tetapi juga tentang apa yang bisa mereka lakukan setelah berada di dalam.

---

##  Analisis Kerentanan

Kerentanan utama bukanlah pada cara mendapatkan akses, melainkan pada kurangnya kontrol yang lebih granula di dalam panel admin itu sendiri. Setelah berhasil masuk sebagai administrator, pengguna diberikan wewenang untuk melakukan tindakan destruktif (menghapus ulasan) tanpa adanya mekanisme pencegahan atau pengawasan tambahan.

Aplikasi ini gagal menerapkan:
1.  **Prinsip Hak Istimewa Terendah (Principle of Least Privilege):** Peran "admin" memiliki terlalu banyak kuasa tanpa pemisahan tugas.
2.  **Logika Bisnis yang Aman:** Tidak ada mekanisme untuk mendeteksi atau mencegah aktivitas yang mencurigakan, seperti penghapusan massal semua ulasan positif yang dapat merusak reputasi platform.

---

##  Tools yang Digunakan

* Web Browser
* Kredensial Akun Administrator (diperoleh dari tantangan sebelumnya)

---

##  Langkah-langkah Penyelesaian (Walkthrough)

1.  **Login sebagai Administrator:** Saya memulai dengan login ke aplikasi menggunakan kredensial admin yang sudah diketahui:
    * **Email:** `admin@juice-sh.op`
    * **Password:** `admin123`

2.  **Mengakses Panel Admin:** Setelah login, saya mengakses halaman administrasi secara langsung melalui URL yang telah ditemukan sebelumnya:
    ```
    http://localhost:3000/#/administration
    ```

3.  **Navigasi ke Manajemen Ulasan:** Di dalam dasbor administrasi, saya menavigasi ke bagian yang mengelola umpan balik dari pengguna. Di Juice Shop, bagian ini bernama **"User Ratings"**.

4.  **Menghapus Ulasan Bintang Lima:** Halaman "User Ratings" menampilkan daftar semua ulasan pengguna beserta rating bintangnya. Saya secara sistematis mengidentifikasi setiap ulasan yang memiliki 5 bintang dan mengklik ikon tong sampah (Delete) di sebelahnya.

5.  **Penyelesaian:** Setelah ulasan terakhir yang memiliki rating 5 bintang dihapus dari sistem, notifikasi keberhasilan menyelesaikan tantangan muncul secara otomatis.

---

##  Rekomendasi Mitigasi

1.  **Terapkan Izin yang Lebih Granular:** Daripada satu peran "admin" yang maha kuasa, pecah hak akses menjadi peran yang lebih spesifik. Misalnya, peran "Moderator Konten" mungkin hanya bisa menandai ulasan, sementara hanya "Super Admin" yang bisa menghapusnya.

2.  **Implementasikan Jejak Audit (Audit Trails):** Catat (log) semua tindakan signifikan yang dilakukan oleh akun admin, terutama tindakan yang bersifat merusak seperti penghapusan data. Ini penting untuk akuntabilitas dan investigasi.

3.  **Gunakan Mekanisme "Soft Delete":** Alih-alih menghapus data secara permanen dari database, tandai data tersebut sebagai "dihapus". Ini memungkinkan pemulihan data jika terjadi kesalahan atau tindakan jahat.

4.  **Terapkan Batasan Logika Bisnis:** Sistem dapat dirancang untuk mendeteksi dan menandai aktivitas yang tidak wajar, seperti jika seorang admin menghapus lebih dari 10 ulasan positif dalam satu menit. Aktivitas seperti ini dapat memicu peringatan atau memerlukan persetujuan tambahan.

---

##  Kesimpulan

Tantangan ini adalah contoh yang baik tentang bagaimana keamanan aplikasi harus melampaui sekadar melindungi "pintu masuk". Sangat penting untuk merancang fungsionalitas di dalam area terproteksi (seperti panel admin) dengan asumsi bahwa bahkan pengguna yang sah pun dapat menyalahgunakan hak akses mereka. Menerapkan kontrol yang cermat dan jejak audit di dalam panel admin adalah kunci untuk mencegah penyalahgunaan fungsionalitas.
<img width="1920" height="1080" alt="Screenshot (18)" src="https://github.com/user-attachments/assets/b5503315-0c22-460a-94a1-d821fd475772" />
<img width="1920" height="1080" alt="Screenshot (19)" src="https://github.com/user-attachments/assets/3e876517-6f3a-4fb6-a95a-9b659cbdbd36" />

