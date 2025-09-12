# Write-Up: OS Command Injection, Simple Case

## Informasi Lab
* Nama Lab: OS command injection, simple case
* Tingkat: APPRENTICE
* Tujuan: Mengidentifikasi dan mengeksploitasi kerentanan OS Command Injection untuk menjalankan perintah whoami pada sistem target dan mengungkap nama pengguna saat ini.

## Ringkasan Kerentanan
Titik injeksi berada pada fitur "Check stock" di halaman detail produk. Ketika pengguna mengirimkan permintaan untuk memeriksa stok, aplikasi di backend kemungkinan menjalankan sebuah skrip shell dengan parameter yang dikontrol oleh pengguna.

Perintah hipotetis yang mungkin berjalan di backend server bisa terlihat seperti ini:
` /home/user/scripts/check-stock.sh [productId] [storeId] `

## Langkah-langkah Eksploitasi
* Identifikasi Endpoint: Dengan menggunakan fitur "Check stock" di browser yang terhubung ke Burp Proxy, kami mengidentifikasi endpoint yang rentan, yaitu POST /product/stock.

## Injeksi Payload
* whoami

Body permintaan HTTP diubah menjadi:
* productId=1&storeId=1 | whoami

## Penyelesaian
* Respons dari server akan menampilkan output dari perintah whoami, yaitu nama pengguna. Dalam kasus ini, nama pengguna yang ditemukan adalah peter-1v7znUv.

## Bukti
<img width="1258" height="627" alt="image" src="https://github.com/user-attachments/assets/e21ce414-4259-4499-9ba6-a99c34fdbf06" />
<img width="1250" height="368" alt="image" src="https://github.com/user-attachments/assets/211fcab7-d75e-477c-9684-6990ff4f76f1" />
<img width="1198" height="497" alt="image" src="https://github.com/user-attachments/assets/d61c1298-aed1-4345-9a7a-f449858cb492" />
<img width="1110" height="551" alt="image" src="https://github.com/user-attachments/assets/74e4db0c-1919-42c5-9344-8ca78ca1f2b5" />




