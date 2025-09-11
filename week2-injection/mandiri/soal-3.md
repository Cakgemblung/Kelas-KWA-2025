# Write-Up: SQL Injection - Lab Login Bypass

## Informasi Lab

*   **Nama Lab**: SQL injection vulnerability allowing login bypass
*   **Tingkat** : Apprentice
*   **Vulnerability**: SQL Injection

## Ringkatan Kerentanan
Aplikasi web memiliki kerentanan SQL Injection pada form login. Input yang dimasukkan pengguna pada kolom email/username tidak divalidasi atau disanitasi dengan benar sebelum digunakan dalam query database. 

## Analisis Kerentanan
Titik masuk kerentanan berada pada kolom input di halaman login. Di backend, aplikasi kemungkinan besar menjalankan query SQL yang strukturnya mirip seperti ini untuk memverifikasi kredensial pengguna:

` SELECT * FROM users WHERE username = '[INPUT_USERNAME]' AND password = '[INPUT_PASSWORD]' `

## Langkah langkah Ekssploitasi
Metode eksploitasi yang paling andal adalah dengan menggunakan Burp Suite untuk memanipulasi permintaan HTTP secara langsung.

` Payload: administrator'-- `

Bagian body dari permintaan HTTP diubah dari:

` mailAddress=test&Password=test`

Menjadi:

` EmailAddress=administrator'-- &Password=test `

## Penjelasan Detail Payload
Payload administrator'--  bekerja dengan cara memanipulasi query SQL di backend.

administrator: Ini adalah nama pengguna yang valid yang ingin kita akses. Query akan mulai mencari baris data milik pengguna ini.

' (Tanda Kutip Tunggal): Karakter ini berfungsi untuk "menutup" string nama pengguna secara prematur. Query yang tadinya mengharapkan WHERE username = '...' sekarang menjadi WHERE username = 'administrator'.

--  (Dua Tanda Hubung dan Spasi): Ini adalah sintaks untuk memulai komentar dalam banyak dialek SQL. Semua teks setelah tanda ini dalam satu baris akan diabaikan oleh database. Akibatnya, bagian pengecekan kata sandi (AND password = '...') tidak pernah dieksekusi.

## Transformasi Query:
Query Asli yang Diharapkan Server:

* SELECT * FROM users WHERE username = 'administrator'-- ' AND password = 'test'

Query yang Sebenarnya Dieksekusi Database:

* SELECT * FROM users WHERE username = 'administrator'

## Bukti

<img width="1244" height="652" alt="image" src="https://github.com/user-attachments/assets/be6582fc-2e87-4f7d-8a41-87660c0a635c" />
<img width="1230" height="660" alt="image" src="https://github.com/user-attachments/assets/23723410-4a04-4a32-bfd7-c29ddb81c794" />
<img width="1241" height="664" alt="image" src="https://github.com/user-attachments/assets/fa478d1f-a3f2-4f13-aac4-2c37e45aac51" />
<img width="1252" height="478" alt="image" src="https://github.com/user-attachments/assets/72a76679-a72e-4361-9c5c-18590350cdbb" />
<img width="1246" height="665" alt="image" src="https://github.com/user-attachments/assets/84146f76-707f-4209-b2b9-f5ec14bf0584" />

