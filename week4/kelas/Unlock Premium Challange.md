# Juice-Shop Write-Up: Unlock Premium Challenge

## 1. Gambaran Tantangan

- **Judul:** Unlock Premium Challenge
- **Kategori:** Cryptographic Issues
- **Tingkat Kesulitan:** ⭐⭐⭐⭐⭐⭐ (6 dari 6)

Tantangan ini menguji kemampuan untuk mengeksploitasi manajemen kunci enkripsi yang buruk. Tujuannya adalah untuk menemukan kunci enkripsi dan IV yang terekspos, menggunakannya untuk mendekripsi sebuah URL rahasia dari data yang ditemukan di kode sumber, dan akhirnya mengakses konten "premium" yang terkunci di balik *paywall* simulasi.

---

## 2. Metodologi dan Solusi

Solusi untuk tantangan ini terdiri dari tiga tahap utama: menemukan aset kriptografi, menemukan data terenkripsi, dan melakukan dekripsi untuk mengungkap konten rahasia.

### Langkah 1: Menemukan Kunci Enkripsi yang Bocor

Saya memulai dengan mencari kemungkinan adanya kesalahan konfigurasi pada server, khususnya direktori yang terekspos.

1.  Saya berhasil menemukan direktori `/encryptionkeys` yang dapat diakses secara publik.
2.  Di dalamnya, terdapat file `premium.key`.
3.  Setelah menganalisis file tersebut, saya mendapatkan komponen-komponen penting untuk dekripsi AES:
    * **Kunci (Key):** `EA99A61D92D2955B1E9285B55BF2AD42`
    * **Initialization Vector (IV):** `1337133713371337`

### Langkah 2: Menemukan Data Terenkripsi

Selanjutnya, saya mencari data yang dienkripsi menggunakan kunci di atas.

1.  Saya memeriksa kode sumber (View Source) dari halaman utama aplikasi.
2.  Di sana, saya menemukan sebuah komentar HTML yang berisi string Base64 yang panjang. Ini adalah data terenkripsi yang saya cari.
    * **Data Terenkripsi (Base64):** `IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==`

### Langkah 3: Dekripsi Data dan Akses Konten

Dengan Key, IV, dan data terenkripsi, saya menggunakan **OpenSSL** untuk melakukan dekripsi.

1.  Saya membuka terminal dan menjalankan perintah berikut:
    ```bash
    echo "IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==" | openssl enc -d -aes-256-cbc -K EA99A61D92D2955B1E9285B55BF2AD42 -iv 1337133713371337 -a -A
    ```
2.  Perintah ini berhasil mendekripsi data dan menghasilkan URL berikut:
    `http://localhost:3000/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us`
3.  Dengan mengunjungi URL tersebut, tantangan berhasil diselesaikan.

---

## 3. Kesimpulan dan Pembelajaran

Tantangan ini adalah contoh klasik dari risiko keamanan akibat manajemen aset yang buruk. Meskipun enkripsi yang kuat (AES-256) digunakan, keamanannya menjadi sia-sia karena kunci dan IV disimpan di lokasi yang dapat diakses publik.

Pelajaran utama dari tantangan ini adalah:
* **Amankan Aset Kriptografi:** Kunci enkripsi, sertifikat, dan rahasia lainnya harus disimpan dengan aman menggunakan solusi seperti *vault* atau *key management service* (KMS) dan tidak boleh dapat diakses dari web.
* **Terapkan Kontrol Akses:** Direktori dan file sensitif pada server web harus dilindungi dengan aturan kontrol akses yang ketat untuk mencegah penjelajahan direktori (directory traversal/listing).
* **Keamanan Berlapis:** Keamanan tidak boleh hanya bergantung pada satu lapisan. Enkripsi yang kuat harus dipadukan dengan manajemen kunci yang aman dan kontrol akses yang solid.

---

## Bukti Penyelesaian

<img width="1920" height="1080" alt="Screenshot (72)" src="https://github.com/user-attachments/assets/c69acc59-6ef9-4bd4-98ef-047120ad489b" />
<img width="1920" height="1080" alt="Screenshot (73)" src="https://github.com/user-attachments/assets/fb4381a6-3ed6-4803-9c50-8d6a4b68df94" />
<img width="1920" height="1080" alt="Screenshot (74)" src="https://github.com/user-attachments/assets/eb1f4f9e-898a-493e-b813-ac71c5c076c1" />
<img width="1920" height="1080" alt="Screenshot (75)" src="https://github.com/user-attachments/assets/5582052e-2e73-4248-9035-347d6b0cca88" />

