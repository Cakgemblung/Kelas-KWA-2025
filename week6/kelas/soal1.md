# Write-up: Error Handling Challenge (OWASP Juice Shop)

## Ringkasan Tantangan

- **Judul:** Error Handling
- **Kategori:** Security Misconfiguration
- **Tingkat Kesulitan:** ⭐ (1/6)
- **Tujuan:** Memprovokasi kesalahan pada aplikasi untuk membocorkan detail teknis backend karena penanganan error yang tidak tepat (verbose error).

---

## Tools yang Digunakan

- **Web Browser:** Chrome, Firefox, atau browser modern lainnya.
- **Pengetahuan Dasar:** Pemahaman tentang cara kerja URL dan respons server terhadap input yang tidak valid.

---

## Metodologi dan Solusi

Metodologi yang digunakan adalah dengan sengaja memberikan input yang tidak dapat diproses oleh server pada level URL parser. Tujuannya adalah untuk memicu error internal, bukan error standar seperti 404 (Not Found) atau 403 (Forbidden).

### Langkah 1: Eksperimen Awal

Percobaan awal bisa dilakukan dengan mengakses path yang tidak ada namun umum, seperti `/admin` atau `/config`, yang biasanya menghasilkan error 404. Namun, untuk tantangan ini, kita perlu error yang lebih mendalam yang berasal dari kegagalan sistem internal.

### Langkah 2: Memprovokasi Error dengan URL Encoding

Teknik yang paling efektif adalah dengan memasukkan karakter URL encoding yang tidak valid atau tidak lengkap. Karakter `%` digunakan untuk mengawali kode encoding (misalnya `%20` untuk spasi), namun jika digunakan sendirian, server tidak akan bisa menerjemahkannya.

**Aksi yang Dilakukan:**
Tambahkan karakter `%` di akhir URL utama aplikasi.

**URL yang Digunakan:**
```
http://localhost:3000/%
```

### Langkah 3: Analisis Respons Error

Setelah mengakses URL di atas, aplikasi akan menampilkan halaman error `400 Bad Request` yang sangat detail.

**Pesan Error yang Diterima:**
> 400 URIError: Failed to decode param '%'

Respons ini membuktikan bahwa tantangan telah berhasil diselesaikan.

---

## Analisis Kebocoran Informasi (Information Leakage)

Halaman error yang muncul sangat berbahaya karena membocorkan detail berikut:

1.  **Versi Framework:** Judul halaman error secara eksplisit menyebutkan **"OWASP Juice Shop (Express ^4.21.0)"**. Ini memberi tahu penyerang teknologi backend (Express.js) dan versi spesifik yang digunakan, memungkinkan mereka mencari celah keamanan yang sudah diketahui untuk versi tersebut.
2.  **Stack Trace Lengkap:** Server menampilkan seluruh jejak tumpukan (stack trace) dari error tersebut. Ini mengungkap struktur direktori internal server (`...juice-shop/node_modules/express/lib/router/index.js`), nama file, dan baris kode yang menyebabkan error.

Informasi ini sangat berharga bagi penyerang untuk merencanakan serangan lebih lanjut.

---

## Rekomendasi Remediasi

Untuk mencegah kebocoran informasi serupa di lingkungan produksi:

1.  **Implementasikan Halaman Error Generik:** Ganti semua halaman error (seperti 400, 403, 404, 500) dengan halaman statis yang ramah pengguna dan tidak memberikan detail teknis apa pun.
2.  **Konfigurasi Middleware Penanganan Error:** Dalam aplikasi berbasis Express.js, pastikan middleware penanganan error dikonfigurasi untuk menangkap semua kesalahan dan menyembunyikan detail sensitif saat aplikasi berjalan dalam mode `production`.
3.  **Sembunyikan Informasi Versi:** Konfigurasikan server web (melalui header HTTP `X-Powered-By` atau `Server`) agar tidak menampilkan nama dan versi software yang digunakan.

---

## Bukti Penyelesaian

Berikut adalah bukti visual dari penyelesaian tantangan.

**Gambar 1: Pesan Error yang Verbose dan Membocorkan Informasi**
<img width="1920" height="1080" alt="Screenshot (91)" src="https://github.com/user-attachments/assets/37d71767-fe1f-4e96-b02e-6325ed94a68f" />
