# Juice-Shop Write-Up: Kupon Palsu (Forged Coupon)

**Kategori:** Cryptographic Issues
**Tingkat Kesulitan:** ⭐⭐⭐⭐⭐⭐ (6 dari 6)

## Ikhtisar Tantangan

Tantangan "Forged Coupon" adalah salah satu tantangan paling kompleks di Juice Shop, yang mengharuskan kita untuk melakukan rekayasa balik (*reverse engineering*) terhadap mekanisme pembuatan kode kupon. Tujuannya adalah untuk memahami logika di baliknya, lalu membuat sendiri kupon palsu yang memberikan diskon minimal 80%.

---

## Metode dan Solusi

Penyelesaian tantangan ini melibatkan serangkaian langkah investigasi, mulai dari eksploitasi celah server hingga mereplikasi algoritma pembuatan kupon.

### 1. Pembongkaran Informasi via Poison Null Byte

Langkah awal adalah mencari petunjuk yang mungkin bocor dari server. Dengan menjelajahi direktori `/ftp`, saya menemukan beberapa file cadangan (`.bak`) yang dilindungi oleh filter file. Filter ini hanya mengizinkan akses ke file `.md` dan `.pdf`.

Untuk melewati batasan ini, saya menggunakan teknik **Poison Null Byte**. Dengan menambahkan `%2500.md` (double URL-encoded null byte) ke akhir nama file, saya berhasil menipu server agar mengabaikan filter dan memberikan akses ke file `.bak` yang terlarang.

Contoh URL eksploitasi:
`http://localhost:3000/ftp/package.json.bak%2500.md`

Dengan cara ini, saya berhasil mengunduh `package.json.bak` dan `coupons_2013.md.bak`.

### 2. Menemukan Algoritma Encoding: Z85

Analisis terhadap file-file yang berhasil diunduh memberikan dua petunjuk krusial:
* **`coupons_2013.md.bak`:** Berisi daftar kode kupon lama yang terlihat acak, seperti `n<MibgC7sn`.
* **`package.json.bak`:** Mengungkap daftar dependensi aplikasi, salah satunya adalah library encoding bernama `Z85`.

Dengan petunjuk ini, saya menggunakan decoder Z85 online (seperti Cryptii) untuk mendekode kupon sampel `n<MibgC7sn`. Hasilnya adalah plaintext yang bisa dibaca: `JAN13-10`.

Ini mengungkap pola rahasia di balik kupon:
> **{Singkatan Bulan 3 Huruf}{Tahun 2 Digit}-{Persentase Diskon}**

### 3. Membuat Kupon Palsu yang Valid

Untuk membuat kupon yang berfungsi, saya perlu mengetahui tanggal saat ini di sisi server. Informasi ini saya dapatkan dengan mendekode **JWT (JSON Web Token)** yang disimpan di cookie browser. Di dalam payload token, terdapat *timestamp* `iat` (*Issued At*).

Dengan mengonversi *timestamp* ini menggunakan Epoch Converter, saya menemukan bahwa tanggal server adalah **September 2025**.

Dengan semua informasi ini, saya menyusun plaintext untuk kupon baru dengan diskon 80%:
* **Plaintext:** `SEP25-80`

Plaintext ini kemudian saya *encode* kembali menggunakan Z85, menghasilkan kode kupon palsu yang valid, seperti `q:<I<rH7Z"w`.

### 4. Verifikasi dan Penyelesaian

Saya menambahkan produk ke keranjang belanja dan memasukkan kode kupon palsu tersebut. Sistem berhasil memvalidasinya, menerapkan diskon 80%, dan tantangan pun selesai.

---

## Kerentanan dan Pencegahan

Kerentanan utama terletak pada penggunaan skema *encoding* (Z85) yang dapat dibalik, bukan *encryption* yang aman secara kriptografis. Siapa pun yang berhasil merekayasa balik skema ini dapat membuat kupon tak terbatas.

**Rekomendasi Pencegahan:**
1.  **Gunakan Tanda Tangan Digital:** Kupon harus ditandatangani secara digital (misalnya, menggunakan HMAC-SHA256) dengan kunci rahasia yang hanya diketahui oleh server. Ini akan membuat kupon tidak dapat dipalsukan.
2.  **Perbaiki Kontrol Akses File:** Mencegah kebocoran file sensitif seperti `.bak` dengan mengonfigurasi server web secara benar dan tidak mengandalkan filter nama file yang rapuh.
3.  **Hindari Mengekspos Informasi Internal:** Informasi seperti dependensi library dan *timestamp* server tidak seharusnya mudah diakses oleh pengguna.

---

## Bukti

<img width="1920" height="1080" alt="Screenshot (76)" src="https://github.com/user-attachments/assets/49775f7e-0f30-4d42-bd28-9e6dd4cb095d" />
<img width="1920" height="1080" alt="Screenshot (77)" src="https://github.com/user-attachments/assets/364b903b-fa40-46d5-83ed-801497ac5994" />
<img width="1920" height="1080" alt="Screenshot (78)" src="https://github.com/user-attachments/assets/4e99ba68-f170-40a8-95ae-57cb6d5d1d91" />
<img width="1920" height="1080" alt="Screenshot (79)" src="https://github.com/user-attachments/assets/53791e28-cb36-48f8-a51a-0e7970be6b63" />
<img width="1920" height="1080" alt="Screenshot (80)" src="https://github.com/user-attachments/assets/d7e8b3e2-752d-4bd0-ad71-305e1f397337" />
