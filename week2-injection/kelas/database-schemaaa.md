## Tantangan: Database Schema

Tantangan ini mengharuskan saya untuk mengambil seluruh definisi skema database melalui eksploitasi SQL Injection. Serangan ini berbeda dari yang sebelumnya karena tujuannya bukan untuk login, melainkan untuk mengambil data internal yang sensitif.

### 1. Menemukan Kerentanan

Kerentanan utama berada pada API pencarian produk. Query di backend tidak memvalidasi input dengan benar, sehingga saya bisa menyuntikkan perintah SQL tambahan.

### 2. Menentukan Jumlah Kolom
Saya perlu mengeteahui berapa banyak kolom yang dihasilkan query dari produk asli
saya menggunakan payload `UNION SELECT`

*   **Tujuan:** Mencoba berbagai jumlah kolom hingga server tidak mengembalikan kesalahan (error).
*   **Payload :**
  
    ```
    q=test')) UNION SELECT 1,2,3,4,5,6,7,8,9-- (Berhasil)
    ```
*   **Hasil:** Saya menemukan bahwa query asli mengembalikan 9 kolom.
