# Write-Up: SSRF (OWASP Juice Shop)

### **Ringkasan Tantangan**

* **Nama:** SSRF (Server-Side Request Forgery)
* **Kategori:** Server-Side Injection
* **Tingkat Kesulitan:** ⭐⭐⭐⭐⭐⭐ (Sangat Sulit)

Tantangan ini bertujuan untuk mengeksploitasi celah keamanan **Server-Side Request Forgery (SSRF)**. Penyerang harus menemukan cara untuk memaksa server aplikasi agar membuat permintaan HTTP ke sebuah endpoint internal (`localhost`) yang tidak dapat diakses dari luar, guna menyelesaikan tantangan.

### **Alat yang Digunakan**

* **Web Browser** (Untuk berinteraksi dengan aplikasi)
* **Text Editor** (seperti Notepad++, untuk memeriksa file)

---

### **Langkah-langkah Penyelesaian**

#### **1. Identifikasi Vektor Serangan**
Titik masuk (vektor) yang paling mungkin untuk SSRF adalah fungsionalitas di mana server mengambil sumber daya dari URL yang disediakan oleh pengguna. Di Juice Shop, fitur untuk mengganti **gambar profil melalui URL** adalah kandidat utamanya.

#### **2. Mengikuti Petunjuk (Information Gathering)**
Sesuai petunjuk tantangan, kita perlu mencari informasi lebih lanjut. Dengan menjelajahi aplikasi, kita bisa menemukan direktori `/ftp/` yang bisa diakses publik. Di dalamnya, terdapat sub-direktori `/quarantine/` yang berisi beberapa file malware.

#### **3. Menemukan Payload**
Kunci dari tantangan ini tersembunyi di dalam salah satu file malware tersebut.
1.  Unduh salah satu file dari direktori `/ftp/quarantine/`.
2.  Buka file tersebut menggunakan Text Editor.
3.  Gunakan fitur pencarian (`Ctrl+F`) untuk mencari kata kunci yang relevan seperti `http` atau `localhost`. Pencarian ini akan mengungkap sebuah URL tersembunyi.

    **URL Payload yang Ditemukan:**
    ```
    http://localhost:3000/solve/challenges/server-side?key=tRy_H4rd3r_n0thIng_iS_Imp0ssibl3
    ```

#### **4. Menjalankan Eksploitasi**
URL `localhost` tidak dapat diakses dari browser kita, namun dapat diakses oleh server itu sendiri. Kita akan menggunakan vektor yang sudah kita identifikasi untuk "menyuruh" server mengakses URL ini.
1.  Login ke akun Juice Shop dan masuk ke halaman **User Profile**.
2.  Salin URL payload yang ditemukan di Langkah 3.
3.  Tempel (paste) URL tersebut ke dalam kolom input **Image URL**.
4.  Klik tombol **Set** untuk menyimpan perubahan.

#### **5. Verifikasi**
Ketika tombol "Set" diklik, server aplikasi akan mencoba mengambil "gambar" dari URL yang kita berikan. Karena URL tersebut adalah endpoint internal untuk menyelesaikan tantangan, server berhasil mengaksesnya, dan notifikasi penyelesaian tantangan SSRF pun muncul.

---

### **Penjelasan Celah Keamanan**

Celah **SSRF** terjadi ketika seorang penyerang dapat memaksa server untuk membuat permintaan HTTP ke domain atau alamat IP pilihan penyerang.

Dalam kasus ini, aplikasi secara naif mempercayai URL yang dimasukkan oleh pengguna tanpa validasi yang memadai. Kita memanfaatkannya dengan memberikan URL `localhost`, yang merupakan alamat internal server. Server, yang memiliki akses penuh ke jaringannya sendiri, menuruti perintah tersebut dan mengakses endpoint rahasia yang tidak seharusnya bisa dijangkau dari internet publik.

### **Rekomendasi Perbaikan**

1.  **Gunakan Allow-list:** Cara paling efektif adalah dengan hanya mengizinkan server untuk mengambil sumber daya dari daftar domain eksternal yang tepercaya (misalnya, `imgur.com`, `gravatar.com`). Tolak semua permintaan ke domain lain.
2.  **Gunakan Block-list:** Blokir secara eksplisit semua permintaan ke alamat-alamat internal yang sensitif, seperti `localhost`, `127.0.0.1`, rentang IP privat (misalnya `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`), dan alamat metadata cloud.
3.  **Validasi Skema URL:** Pastikan hanya skema URL yang aman (seperti `http` dan `https`) yang diizinkan. Tolak skema berbahaya seperti `file://`, `ftp://`, atau `gopher://`.

### **Bukti**
<img width="1920" height="1080" alt="Screenshot (54)" src="https://github.com/user-attachments/assets/69d2f5de-d3c2-4c64-b0be-54621e6af90b" />

<img width="1920" height="1080" alt="Screenshot (55)" src="https://github.com/user-attachments/assets/6184713d-8449-46cb-922c-9144f6d6ca2a" />

<img width="1920" height="1080" alt="Screenshot (56)" src="https://github.com/user-attachments/assets/0cc029d0-b00f-4928-8e86-7a6d6130aedd" />

<img width="1920" height="1080" alt="Screenshot (59)" src="https://github.com/user-attachments/assets/ac5b348e-dcca-47fe-9741-1a5909632049" />

<img width="1920" height="1080" alt="Screenshot (60)" src="https://github.com/user-attachments/assets/1e3346e4-d981-482f-954e-544c09702758" />
