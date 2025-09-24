# Juice-Shop Write-Up: Weird Crypto

## Ringkasan Tantangan

- **Judul:** Weird Crypto
- **Kategori:** Cryptographic Issues
- **Kesulitan:** ⭐⭐ (2 dari 6)

Tantangan ini menguji kemampuan kita untuk mengidentifikasi dan melaporkan penggunaan metode kriptografi yang sudah usang dan tidak aman di dalam aplikasi. Solusinya adalah menemukan bahwa kata sandi pengguna di-hash menggunakan algoritma MD5 yang rentan.

---

## Alat yang Digunakan

* **Peramban Web** (misalnya, Chrome, Firefox) dengan Developer Tools (`F12`).
* **JWT Decoder** (misalnya, situs web [jwt.io](https://jwt.io/)).

---

## Metodologi dan Langkah-Langkah Pengerjaan

Metodologi yang digunakan adalah dengan menganalisis token autentikasi (JWT) yang dihasilkan setelah login untuk menemukan kelemahan kriptografi.

### Langkah 1: Login dan Temukan JWT

1.  Login ke aplikasi OWASP Juice Shop menggunakan akun yang sudah terdaftar.
2.  Buka **Developer Tools** di browser Anda (tekan `F12`).
3.  Pilih tab **Application** (di Chrome) atau **Storage** (di Firefox).
4.  Di panel kiri, navigasikan ke **Cookies** dan pilih URL aplikasi.
5.  Temukan cookie dengan nama `token` dan salin (copy) nilainya. Ini adalah JSON Web Token Anda.

### Langkah 2: Dekode dan Analisis Token

1.  Kunjungi situs [jwt.io](https://jwt.io/).
2.  Tempelkan nilai `token` yang sudah Anda salin ke dalam kolom **Encoded** di sebelah kiri.
3.  Situs akan secara otomatis mendekode token. Perhatikan bagian **Payload** di sebelah kanan.

### Langkah 3: Identifikasi Kerentanan Kriptografi

Di dalam data Payload, Anda akan menemukan informasi pengguna, termasuk sebuah parameter `password`.

```
json
{
  "data": {
    "id": 1,
    "email": "user@example.com",
    "password": "0192023a7bbd73250516f069df18b500",
    "role": "customer",
    ...
  },
  ...
```

### Bukti

<img width="1920" height="1080" alt="Screenshot (62)" src="https://github.com/user-attachments/assets/6a88147b-8914-4291-a8ff-cdc9c1b06f4f" />
<img width="1920" height="1080" alt="Screenshot (64)" src="https://github.com/user-attachments/assets/8de8d1f9-857c-4741-ba8b-a86d06b9816a" />
<img width="1920" height="1080" alt="Screenshot (65)" src="https://github.com/user-attachments/assets/c1a23333-2c9e-4659-bfaf-092f67a3f623" />
