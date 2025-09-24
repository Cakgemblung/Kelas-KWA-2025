# PortSwigger Lab Write-up: JWT Authentication Bypass via Flawed Signature Verification

## 1. Penjelasan Tantangan

- **Link Lab:** `https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification`

Tantangan ini mengeksploitasi kerentanan implementasi JWT di mana server yang dikonfigurasi untuk algoritma asimetris (seperti `RS256`) dapat ditipu untuk memverifikasi token menggunakan algoritma simetris (`HS256`). Celah ini terjadi jika server menggunakan kunci publik sebagai kunci rahasia saat memverifikasi token `HS256`. Tujuannya adalah memalsukan token admin dan menghapus akun `carlos`.

---

## 2. Metodologi dan Eksploitasi

Serangan ini berpusat pada "kebingungan algoritma" (*algorithm confusion*), di mana kita memaksa server untuk menggunakan kunci publik dalam konteks yang salah.

### Langkah 1: Mendapatkan Kunci Publik Server

1.  Login ke lab dengan kredensial `wiener:peter` dan dapatkan token JWT awal menggunakan Burp Suite.
2.  Server yang menggunakan `RS256` sering mengekspos kunci publiknya melalui endpoint **JSON Web Key Set (JWKS)**. Saya mengakses `/.well-known/jwks.json`.
3.  Dari respons JSON, saya menyalin seluruh objek kunci publik yang disediakan oleh server.

### Langkah 2: Memalsukan Token dengan Algoritma HS256

Ini adalah langkah krusial di mana token dimanipulasi dan ditandatangani ulang. Saya menggunakan ekstensi **JWT Editor** di Burp Suite untuk mempermudah proses ini.

1.  Saya mengirim permintaan yang berisi token JWT asli ke **Burp Repeater**.
2.  Di tab ekstensi "JSON Web Token", saya mengubah nilai `sub` di payload dari `wiener` menjadi `administrator`.
3.  Saya melancarkan serangan "HMAC Mismatch". Saya membuat kunci simetris baru dan menempelkan seluruh objek JWK (kunci publik) yang saya dapatkan sebelumnya.
4.  Ekstensi secara otomatis melakukan hal berikut:
    * Mengubah header `alg` dari `RS256` menjadi **`HS256`**.
    * Menandatangani ulang seluruh token menggunakan algoritma `HS256`, dengan **kunci publik RSA sebagai kunci rahasianya**.

### Langkah 3: Mengirimkan Permintaan Bypass

Dengan token yang telah dimanipulasi dan ditandatangani ulang, saya melakukan bypass otentikasi.

1.  Di Burp Repeater, saya mengubah path target menjadi `/admin`.
2.  Saya mengirim permintaan. Server yang rentan menerima token `HS256`, mengambil kunci publik yang terkait, dan keliru menggunakannya sebagai kunci rahasia untuk verifikasi `HS256`. Karena tanda tangan cocok, server memberikan akses admin.

### Langkah 4: Menyelesaikan Lab

Setelah mendapatkan akses, saya menyelesaikan tujuan akhir.

1.  Saya mengubah path target di Repeater menjadi `/admin/delete?username=carlos`.
2.  Saya mengirim permintaan tersebut dengan token yang sama, yang berhasil menghapus akun `carlos` dan menyelesaikan lab.

---

## 3. Mitigasi

Kerentanan ini dapat dicegah dengan praktik implementasi JWT yang ketat:

1.  **Jangan Percayai Header `alg`:** Server harus secara eksplisit menentukan algoritma yang diharapkan untuk verifikasi dan menolak token apa pun yang tidak cocok. Jangan pernah mengizinkan token untuk menentukan algoritma verifikasinya sendiri.
2.  **Pemisahan Kunci:** Gunakan kunci yang berbeda untuk fungsi kriptografi yang berbeda. Kunci yang digunakan untuk `RS256` tidak boleh dapat diakses oleh fungsi yang memverifikasi `HS256`.
3.  **Gunakan Library JWT yang Teruji:** Implementasi JWT yang dibuat sendiri sering kali rentan. Selalu gunakan library standar yang telah teruji keamanannya dan ikuti dokumentasinya dengan cermat.

## Bukti

<img width="1920" height="1080" alt="Screenshot (89)" src="https://github.com/user-attachments/assets/695b0619-0c29-41f2-818b-dd679d7e695f" />
<img width="1920" height="1080" alt="Screenshot (90)" src="https://github.com/user-attachments/assets/8f0dfa6c-0ef9-4b3a-8da0-5c29c37bd427" />
<img width="1920" height="1080" alt="Screenshot (87)" src="https://github.com/user-attachments/assets/f733895a-b06e-4cf3-b42f-3c6f7a1e7a0b" />
<img width="1920" height="1080" alt="Screenshot (86)" src="https://github.com/user-attachments/assets/1fc702d1-c42a-4592-89f3-f4834dfd27e5" />
<img width="1920" height="1080" alt="Screenshot (85)" src="https://github.com/user-attachments/assets/2164c3e3-78f2-405e-ac97-5ad77b9a2d80" />
