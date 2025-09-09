## Tantangan: Login Admin

### Deskripsi Tantangan
Tantangan ini menargetkan akun administrator dan bertujuan untuk mendapatkan akses tidak sah ke dalamnya. Tingkat kesulitannya adalah 2 dari 6 bintang. Serangan ini berfokus pada eksploitasi kerentanan SQL Injection yang ditemukan di halaman login.

*   Tanda kutip tunggal (`'`) berfungsi untuk menutup string email dalam query.
*   Tanda `--` berfungsi sebagai komentar SQL, yang membuat database mengabaikan sisa dari perintah query, termasuk bagian yang seharusnya memeriksa kata sandi.

#### Eksekusi Serangan
1.  Saya memasukkan payload `OR true` pada kolom Email, tetapi masih gagal.
2.  Saya memasukkan lagi payload  `admin@juice-sh.op'--` pada kolom Email.
3.  Pada kolom Password, saya memasukkan teks apa pun karena tidak berpengaruh pada system 
4.  Setelah menekan tombol "Log in," serangan berhasil.

### Bukti
<img width="1920" height="1080" alt="Screenshot (574)" src="https://github.com/user-attachments/assets/319d05fb-5ff3-40c8-90b7-eb26214edd2e" />

<img width="1920" height="1080" alt="Screenshot (576)" src="https://github.com/user-attachments/assets/3be1ab2f-d006-4552-9c1d-6a60a2483ef7" />

<img width="1920" height="1080" alt="Screenshot (579)" src="https://github.com/user-attachments/assets/ea2c5b62-7edf-4709-9cec-1441bb8d22b3" />
