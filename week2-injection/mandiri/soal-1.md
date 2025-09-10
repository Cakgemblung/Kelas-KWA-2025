# Write-Up: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

## Informasi lab
* Nama Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

* Tingkat: APPRENTICE

* Tujuan: Melakukan serangan SQL Injection untuk menampilkan produk yang tidak dirilis (unreleased products) dari database.

## Ringkasan Keamanan
Aplikasi web memiliki fitur filter kategori produk yang rentan terhadap serangan SQL Injection. Parameter kategori yang dikirim oleh pengguna tidak divalidasi atau disanitasi dengan benar sebelum digabungkan ke dalam query SQL.

## Analisis Kerentanan
Query asli yang dijalankan oleh server adalah sebagai berikut: `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`
Tujuan kita adalah mengabaikan kondisi AND released = 1 untuk melihat semua produk.

## Pembuatan dan Injeksi Payload
Untuk menampilkan data tersembunyi (produk dengan released = 0), kita perlu membuat kondisi WHERE pada query menjadi selalu benar atau mengomentari bagian yang membatasi hasil.

Payload yang Digunakan: `' OR 1=1--`

# Proses Injection
Payload disuntikkan langsung ke nilai parameter category pada URL. URL yang sudah dimodifikasi menjadi:
* https://<id-lab>.web-security-academy.net/filter?category=Gifts' OR 1=1--

# Verifikasi Hasil 
Setelah menekan Enter dengan URL yang telah dimodifikasi, server akan memproses permintaan tersebut.
* Aplikasi akan menampilkan daftar lengkap produk, termasuk produk yang sebelumnya tidak terlihat.

* Sebuah notifikasi "Congratulations, you solved the lab!" akan muncul, menandakan bahwa tujuan lab telah tercapai.

## Bukti
<img width="1920" height="1080" alt="Screenshot (589)" src="https://github.com/user-attachments/assets/e6d23492-6482-4afc-8b06-2529c2c50b35" />
<img width="1920" height="1080" alt="Screenshot (590)" src="https://github.com/user-attachments/assets/68fabe53-d2b8-4f7c-8928-565088b383de" />
<img width="1920" height="1080" alt="Screenshot (591)" src="https://github.com/user-attachments/assets/88807c82-8fc9-43c3-9030-b1e0c9830757" />
