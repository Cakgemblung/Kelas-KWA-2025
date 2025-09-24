# Juice-Shop Write-Up: Nested Easter Egg

## Ringkasan Tantangan

- **Judul:** Nested Easter Egg
- **Kategori:** Cryptographic Issues
- **Kesulitan:** ⭐⭐⭐⭐ (4 dari 6)

Tantangan ini mengharuskan kita untuk menemukan pesan rahasia yang tersembunyi di dalam *Easter Egg* lain. Solusinya melibatkan proses dekripsi berlapis, yaitu Base64 diikuti oleh ROT13, untuk mengungkap sebuah path URL tersembunyi.

---

## Alat yang Digunakan

* **Peramban Web** (misalnya, Chrome, Firefox) dengan Developer Tools.
* **Decoder Base64 Online** (misalnya, `base64decode.org`).
* **Dekriptor ROT13 Online** (misalnya, `rot13.com`).

---

## Metodologi dan Langkah-Langkah Pengerjaan

Solusi tantangan ini mengikuti alur dekripsi dua langkah untuk mengungkap sebuah URL rahasia.

### Langkah 1: Menemukan dan Mendekode Pesan Base64

1.  Sebagai prasyarat, kita harus terlebih dahulu menyelesaikan tantangan "Easter Egg" dengan mengakses direktori `/ftp` dan mengunduh file `eastere.gg`.
2.  File tersebut berisi string yang dienkode dengan Base64:
    ```
    L2d1ci9xcmlmL25lci9mYi9zaGFhbC9ndXJsL3V2cS9uYS9ybmZncmUvcnR0L2p2Z3V2YS9ndXIvcm5mZ3JlL3J0dA==
    ```
3.  Menggunakan decoder Base64, string di atas diterjemahkan menjadi:
    ```
    /gur/qnf/ner/fb/shaal/gur/uvqva/na/rnfgre/rtt/jvguhva/gur/gur/rnfgre/rtt
    ```

### Langkah 2: Mendekripsi Pesan ROT13

1.  Teks hasil dekode Base64 di atas terlihat acak, tetapi polanya cocok dengan **ROT13 cipher**, di mana setiap huruf digeser 13 posisi.
2.  Dengan menggunakan dekriptor ROT13, teks tersebut diubah menjadi path URL yang dapat dibaca:
    ```
    /the/devs/are/so/funny/they/hid/an/easter/egg/within/the/easter/egg
    ```

### Langkah 3: Mengakses URL Tersembunyi

1.  Langkah terakhir adalah mengunjungi path URL yang telah berhasil didekripsi.
2.  Akses URL lengkapnya di browser: `http://<alamat-juice-shop>/the/devs/are/so/funny/they/hid/an/easter/egg/within/the/easter/egg`.
3.  Setelah mengunjungi halaman tersebut, tantangan "Nested Easter Egg" akan otomatis ditandai sebagai selesai.

---

## Rekomendasi Perbaikan

Meskipun ini adalah *easter egg*, jika data sensitif dilindungi dengan cara ini, maka ini adalah kerentanan.

1.  **Hindari Kriptografi yang Lemah:** Jangan pernah menggunakan cipher yang mudah dipecahkan seperti ROT13 untuk menyembunyikan informasi apa pun.
2.  **Gunakan Algoritma Enkripsi Standar:** Untuk data yang perlu dirahasiakan, gunakan algoritma enkripsi yang kuat dan teruji seperti AES-256.

---

## Bukti Penyelesaian

<img width="1920" height="1080" alt="Screenshot (68)" src="https://github.com/user-attachments/assets/bc22edeb-b234-4fa9-ba57-f551c0e7842f" />
<img width="1920" height="1080" alt="Screenshot (69)" src="https://github.com/user-attachments/assets/1ff94372-88dd-4dae-b7d1-14f25037681d" />
<img width="1920" height="1080" alt="Screenshot (70)" src="https://github.com/user-attachments/assets/b971b2a1-4980-482b-ab4f-e285b5253951" />
<img width="1920" height="1080" alt="Screenshot (71)" src="https://github.com/user-attachments/assets/5f6001bf-689d-478a-a0ac-f45ef82051ca" />
