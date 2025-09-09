## Tantangan: Login Jim

### Deskripsi Tantangan
Tantangan ini meminta kita untuk masuk ke akun pengguna "Jim" dengan cara mengeksploitasi kerentanan SQL Injection yang sama seperti tantangan sebelumnya. Tingkat kesulitannya adalah 3 dari 6 bintang, sedikit lebih sulit karena email Jim tidak tersedia secara langsung.

### Strategi dan Eksekusi Serangan

#### Pengumpulan Informasi (Reconnaissance)
1. Karena email dari jim tidak di ketahui, saya melakukan pencarian di halaman produk di bagian ulasan pelanggan
2. saya berhasil mendapatkan pengguna "Jim" yang menulis ulasan dan mendapatkan alamat emailnya: `jim@juice-sh.op`.

#### Eksekusi Serangan
1.  Saya logout dari akun sebelumnya.
2.  Saya membuka halaman login.
3.  Pada kolom Email, saya memasukkan payload `jim@juice-sh.op'--`.
4.  Saya memasukkan kata sandi acak pada kolom Password.
5.  Setelah menekan tombol "Log in," serangan berhasil dieksekusi.

# Bukti
<img width="1920" height="1080" alt="Screenshot (583)" src="https://github.com/user-attachments/assets/0285bf42-d61f-43c8-b16f-5f3a511e0d8a" />

<img width="1920" height="1080" alt="Screenshot (584)" src="https://github.com/user-attachments/assets/6bf3b627-c8cf-4670-bc10-47a9a845a7a4" />

<img width="1920" height="1080" alt="Screenshot (585)" src="https://github.com/user-attachments/assets/e3d3ce33-ccf0-4d81-a5de-16d019202b0e" />
