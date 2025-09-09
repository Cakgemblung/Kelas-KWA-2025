### Deskripsi Tantangan
Tantangan ini memiliki tingkat kesulitan 3 dari 6 bintang dan bertujuan untuk masuk ke akun pengguna "Bender" dengan mengeksploitasi kerentanan SQL Injection. Serangan ini memerlukan identifikasi email Bender terlebih dahulu, lalu mem-bypass mekanisme otentikasi.

#### Pengumpulan Informasi
1. pertama sesuai tutorial saya mencari di ulasan produk dan menemukan email dari bender
2. lalu ada juga di panel administrasi, dimana ada data tersimpan
3. Email yang ditemukan adalah `bender@juice-sh.op`.

### Payload
1.  `bender@juice-sh.op'--`: Tanda kutip tunggal (') menutup string email, dan `--` mengomentari sisa query, termasuk bagian verifikasi kata sandi.
2.  `bender@juice-sh.op' OR '1'='1' --`: Payload ini menambahkan kondisi yang selalu bernilai benar (`'1'='1'`) untuk memastikan query berhasil, sambil tetap mengomentari sisa query.

#### Eksekusi Serangan
1. pertama log out dari akun yang sudah login
2. Buka halaman login untuk mencoba dengan email bender
3. halaman password saya isi dengan -- karena tidak berpengaruh pada sistem
4. setelah menekan tombol "login" serangan berhasil

### Bukti 
<img width="1920" height="1080" alt="Screenshot (580)" src="https://github.com/user-attachments/assets/68817bf8-8ea0-4cac-8936-a1d35baeda4b" />

<img width="1920" height="1080" alt="Screenshot (581)" src="https://github.com/user-attachments/assets/320482d7-a091-4e99-93b6-572ce9bdef92" />

<img width="1920" height="1080" alt="Screenshot (582)" src="https://github.com/user-attachments/assets/c0d2a07d-7d02-4482-b062-2f4d49b854ea" />
