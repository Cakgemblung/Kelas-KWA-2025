# Lab Write-Up: Unprotected Admin Functionality

- **Platform:** PortSwigger Web Security Academy
- **Tingkat Kesulitan:** Apprentice
- **Link Lab:** `https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality`

---

##  Ringkasan

Lab ini bertujuan untuk menemukan dan mengeksploitasi kerentanan **Broken Access Control**. Sebuah panel admin fungsional ada di server, namun tidak dilindungi oleh mekanisme autentikasi apa pun, sehingga siapa pun yang mengetahui URL-nya dapat mengaksesnya. Tujuannya adalah untuk mengakses panel ini dan menghapus pengguna bernama `carlos`.

---

##  Analisis Kerentanan

Kerentanan utama adalah kegagalan aplikasi untuk menerapkan kontrol akses pada endpoint administratif. Pengembang menyembunyikan path ke panel admin (`/administrator-panel`) alih-alih mengamankannya. Lokasi path ini secara tidak sengaja dibocorkan melalui file `robots.txt`, yang memungkinkan penyerang untuk menemukannya dengan mudah.

---

##  Tools yang Digunakan

* Web Browser (Firefox, Chrome, dll.)
* Burp Suite (Opsional, untuk analisis traffic)

---

##  Langkah-langkah Penyelesaian (Walkthrough)

### 1. **Pengintaian: Menemukan Path Panel Admin**

Langkah pertama adalah mencari petunjuk. File `robots.txt` adalah tempat yang bagus untuk memulai, karena sering kali berisi path yang seharusnya tidak diindeks oleh mesin pencari.

Saya mengakses `https://<ID-LAB>.web-security-academy.net/robots.txt` dan menemukan konten berikut:

```
User-agent: *
Disallow: /administrator-panel
```

Baris `Disallow` secara eksplisit memberitahu kita lokasi panel admin.

### 2. **Akses: Membuka Panel Admin**

Dengan informasi dari `robots.txt`, saya langsung menavigasi ke URL panel admin:

```
https://<ID-LAB>.web-security-academy.net/administrator-panel
```

Tanpa diminta login, halaman panel admin berhasil terbuka dan menampilkan daftar pengguna aplikasi. Ini mengonfirmasi bahwa endpoint tersebut tidak terproteksi.



### 3. **Eksploitasi: Menghapus Pengguna `carlos`**

Sesuai tujuan lab, saya mencari pengguna `carlos` di dalam daftar dan mengklik tombol **Delete**. Tindakan ini mengirimkan permintaan `GET` ke server untuk menghapus pengguna tersebut.

```http
GET /administrator-panel/delete?username=carlos HTTP/2
Host: <ID-LAB>.web-security-academy.net
Cookie: ...
...
```

Setelah permintaan berhasil diproses, lab menampilkan banner konfirmasi penyelesaian.

---

##  Rekomendasi Mitigasi

Untuk memperbaiki kerentanan ini, beberapa langkah harus diambil:

1.  **Implementasikan Autentikasi:** Semua endpoint administratif harus mewajibkan pengguna untuk login terlebih dahulu.
2.  **Terapkan Otorisasi Berbasis Peran:** Setelah login, server harus memverifikasi bahwa pengguna memiliki peran (*role*) sebagai administrator sebelum memberikan akses.
3.  **Konfigurasi Kontrol Akses Terpusat:** Gunakan *framework* atau *middleware* untuk menerapkan pemeriksaan akses secara konsisten di semua endpoint sensitif.

---

##  Kesimpulan

Lab ini berhasil diselesaikan dengan mengidentifikasi dan mengakses panel admin yang tidak terproteksi. Ini adalah contoh klasik dari kerentanan **Broken Access Control**, yang menyoroti pentingnya mengamankan fungsionalitas sensitif secara aktif, bukan hanya dengan menyembunyikannya.

## Bukti
<img width="1920" height="1080" alt="Screenshot (595)" src="https://github.com/user-attachments/assets/1d068556-9341-4125-a926-21f034996ad4" />
<img width="1920" height="1080" alt="Screenshot (592)" src="https://github.com/user-attachments/assets/3fce962e-408f-4275-8b5a-9e81eb51289a" />
<img width="1920" height="1080" alt="Screenshot (593)" src="https://github.com/user-attachments/assets/f443fee7-56be-496d-b8b4-7f7339b4b3e0" />
<img width="1920" height="1080" alt="Screenshot (594)" src="https://github.com/user-attachments/assets/00efbf05-5757-4add-b301-90bd6460a935" />

