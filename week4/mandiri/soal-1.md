# PortSwigger Lab Write-up: JWT Authentication Bypass via Unverified Signature

## 1. Penjelasan Tantangan

Tantangan ini menguji pemahaman tentang kerentanan **JWT (JSON Web Token)**, khususnya saat server gagal memverifikasi tanda tangan (`signature`) token. Tujuannya adalah untuk mendapatkan akses ke panel `/admin` dengan memanipulasi JWT, dan kemudian menghapus akun `carlos` untuk menyelesaikan lab.

- **Link Lab:** [JWT authentication bypass via unverified signature](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature)

---

## 2. Metodologi dan Eksploitasi

Eksploitasi ini memanfaatkan kelemahan implementasi di mana server memproses token meskipun tanda tangannya tidak valid atau bahkan tidak ada, asalkan header `alg` diatur ke `none`.

### Langkah 1: Menganalisis dan Mengambil JWT

Langkah pertama adalah mendapatkan token JWT yang valid sebagai titik awal.

1.  Akses lab dan login dengan kredensial yang diberikan: `wiener:peter`.
2.  Gunakan **Burp Suite** untuk mencegat lalu lintas. Di tab **Proxy > HTTP history**, temukan permintaan `GET /my-account`.
3.  Di dalam header `Cookie` pada permintaan ini, salin seluruh nilai token `session` yang merupakan JWT.

### Langkah 2: Memanipulasi JWT

Dengan token di tangan, kita memanipulasi *payload* dan *header* untuk mendapatkan akses admin.

1.  Salin token ke alat eksternal seperti **[jwt.io](https://jwt.io/)**.
2.  Di `jwt.io`, ubah bagian **Header**. Ganti nilai `alg` dari `RS256` (atau algoritma lainnya) menjadi `none`.
    ```json
    {
      "alg": "none"
    }
    ```
3.  Di bagian **Payload**, ubah nilai `sub` (subject/username) dari `wiener` menjadi `administrator`.
    ```json
    {
      "sub": "administrator",
      "iat": ...
    }
    ```
4.  Salin token baru yang dihasilkan. Token ini hanya akan memiliki dua bagian (Header dan Payload) karena tanda tangan telah dihapus.

### Langkah 3: Mengirimkan Permintaan untuk Bypass Otentikasi

Token yang telah dimanipulasi sekarang digunakan untuk membuat permintaan palsu ke panel admin.

1.  Di Burp Suite, kirim permintaan `GET /my-account` ke **Repeater**.
2.  Di tab Repeater, ubah baris permintaan menjadi `GET /admin`.
3.  Pada header `Cookie`, ganti nilai token `session` yang lama dengan token baru yang sudah dimanipulasi.
4.  Kirim permintaan tersebut. Respons yang berhasil akan memiliki status `200 OK` dan menampilkan konten panel admin, termasuk tautan untuk menghapus pengguna.

### Langkah 4: Menyelesaikan Tantangan

Akses ke panel admin adalah bukti eksploitasi, tetapi lab baru selesai setelah akun `carlos` dihapus.

1.  Di tab Repeater yang sama, modifikasi permintaan terakhir. Ubah URL target menjadi `/admin/delete?username=carlos`.
2.  Kirim permintaan tersebut. Server akan memprosesnya karena token yang digunakan dianggap sebagai token admin yang sah.
3.  Setelah permintaan berhasil, tantangan akan ditandai selesai.

---

## 3. Mitigasi

Kerentanan ini terjadi karena implementasi server yang tidak aman. Untuk mencegahnya:

* **Selalu Verifikasi Tanda Tangan:** Server **wajib** memverifikasi tanda tangan pada setiap token yang diterima terhadap kunci rahasia yang dimilikinya.
* **Gunakan Whitelist Algoritma:** Server tidak boleh secara buta memercayai algoritma yang ditentukan di header `alg` token. Sebaliknya, server harus memiliki daftar algoritma yang diizinkan (misalnya, hanya `RS256`) dan menolak token yang menggunakan algoritma lain.
* **Tolak Algoritma `none`:** Algoritma `none` harus secara eksplisit ditolak di sisi server.

---

## Bukti

<img width="1920" height="1080" alt="Screenshot (86)" src="https://github.com/user-attachments/assets/52a71375-80b2-4fe6-af58-d7e374bb3a2a" />
<img width="1920" height="1080" alt="Screenshot (87)" src="https://github.com/user-attachments/assets/07e5dde9-54d0-4ba1-b408-782b96314a00" />
<img width="1920" height="1080" alt="Screenshot (85)" src="https://github.com/user-attachments/assets/2c2249df-f938-4b34-9272-38f62934714f" />
<img width="1920" height="1080" alt="Screenshot (83)" src="https://github.com/user-attachments/assets/8c5f9eb2-e8b2-4c57-9f42-5bfba6f2ad01" />
<img width="1920" height="1080" alt="Screenshot (84)" src="https://github.com/user-attachments/assets/a0b143e9-0648-4b8f-847d-699564282afa" />
<img width="1920" height="1080" alt="Screenshot (88)" src="https://github.com/user-attachments/assets/afdfa3e3-2eb4-4292-96b2-af2c1f145796" />

