# Write-Up Challenge: Web3 Sandbox (OWASP Juice Shop)

- **Platform:** OWASP Juice Shop
- **Kategori Tantangan:** Broken Access Control
- **Tingkat Kesulitan:**  (1/6)

---

##  Ringkasan

Tantangan "Web3 Sandbox" mengharuskan peserta untuk menemukan sebuah halaman tersembunyi yang berisi *sandbox* untuk pengembangan dan pengujian *smart contract* Web3. Kerentanan utama yang dieksploitasi adalah **Broken Access Control**, di mana fungsionalitas yang seharusnya hanya untuk developer internal dibiarkan dapat diakses oleh publik tanpa adanya mekanisme autentikasi atau otorisasi yang memadai.

---

##  Analisis Kerentanan

Aplikasi Juice Shop memiliki sebuah *endpoint* atau halaman (`/#/web3-sandbox`) yang tidak tertaut di mana pun dalam antarmuka pengguna standar. Halaman ini berisi alat pengembangan yang fungsional. Kelemahan keamanannya adalah tidak adanya pemeriksaan hak akses (kontrol akses) di sisi server untuk *endpoint* ini.

Aplikasi hanya mengandalkan fakta bahwa URL tersebut "tersembunyi" (*security through obscurity*) sebagai metode perlindungan, yang mana bukanlah sebuah praktik keamanan yang efektif. Siapa pun yang mengetahui atau berhasil menebak URL tersebut dapat langsung mengaksesnya.

---

##  Tools yang Digunakan

* Web Browser

---

##  Langkah-langkah Penyelesaian (Walkthrough)

1.  **Identifikasi Potensi Path:** Berdasarkan nama tantangan, saya mengasumsikan ada halaman terkait "sandbox" atau "web3". Langkah pertama adalah mencoba menebak *path* URL yang umum digunakan untuk lingkungan pengembangan.

2.  **Manipulasi URL Langsung:** Saya mengakses aplikasi Juice Shop yang berjalan di `http://localhost:3000`. Kemudian, saya memanipulasi URL secara langsung di *address bar* browser.

    * URL Awal: `http://localhost:3000/#/`
    * URL Tujuan: `http://localhost:3000/#/web3-sandbox`

    Saya menambahkan path `/web3-sandbox` setelah tanda `#` pada URL.

3.  **Akses dan Penyelesaian:** Setelah menekan Enter, browser langsung membuka halaman "Web3 Sandbox". Halaman ini berisi editor kode, *compiler*, dan fitur lainnya. Saat halaman berhasil dimuat, aplikasi secara otomatis menandai tantangan ini sebagai selesai.

---

##  Rekomendasi Mitigasi

1.  **Terapkan Kontrol Akses Berbasis Peran (Role-Based Access Control):** Semua *endpoint* yang sensitif atau bersifat administratif, termasuk alat developer, harus dilindungi oleh pemeriksaan otorisasi di sisi server. Hanya pengguna dengan peran (misalnya, 'admin' atau 'developer') yang sesuai yang boleh mengakses halaman tersebut.

2.  **Pemisahan Lingkungan (Environment Segregation):** Alat-alat untuk pengembangan dan *debugging* seharusnya tidak pernah ada di dalam *build* aplikasi versi produksi. Gunakan *build flags* atau variabel lingkungan untuk memastikan fitur-fitur tersebut dihilangkan sepenuhnya saat aplikasi di-*deploy* ke server produksi.

3.  **Hindari *Security Through Obscurity*:** Jangan pernah mengandalkan kerahasiaan URL sebagai mekanisme keamanan. Semua *endpoint* harus dianggap dapat ditemukan oleh penyerang.

---

##  Kesimpulan

Tantangan ini adalah contoh klasik dari risiko keamanan yang muncul ketika alat internal atau fitur pengembangan secara tidak sengaja terekspos di lingkungan produksi. Ini menekankan pentingnya menerapkan kontrol akses yang ketat untuk semua bagian aplikasi dan menjaga kebersihan *build* produksi dari komponen yang tidak diperlukan.

## Bukti
<img width="1920" height="1080" alt="Screenshot (14)" src="https://github.com/user-attachments/assets/a0f2a2c0-87c6-4955-86f7-0de13c708134" />
