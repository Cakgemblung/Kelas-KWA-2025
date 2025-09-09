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

### 3. Mengambil Skema Database

Setelah mengetahui jumlah kolom, saya bisa menyusun payload untuk mengambil skema database. Di database SQLite yang digunakan oleh aplikasi, informasi skema disimpan di tabel bernama `sqlite_master` atau `sqlite_schema`. Saya akan mengganti salah satu kolom dengan kolom yang berisi informasi skema.

*   **Tujuan:** Mengambil data dari tabel `sqlite_schema`, khususnya kolom `sql` yang berisi definisi skema (`CREATE TABLE...`).
*   **Payload yang Digunakan:**
    ```sql
    test')) UNION SELECT 1,2,3,4,5,6,7,8,sql FROM sqlite_schema--
    ```
*   **Penjelasan Payload:**
    *   **`test'))`**: Digunakan untuk menutup query pencarian asli.
    *   **`UNION SELECT 1,2,...,sql`**: Menggabungkan query saya dengan query asli. Saya menggunakan `sql` di posisi ke-9 untuk menampilkan definisi skema.
    *   **`FROM sqlite_schema`**: Menunjukkan dari mana data akan diambil.
    *   **`--`**: Menandai sisa query asli sebagai komentar.
*   **Eksekusi:** Saya mengakses URL dengan payload yang sudah di-encode:
    ```
    http://127.0.0.1:3000/rest/products/search?q=test'))%20UNION%20SELECT%201,2,3,4,5,6,7,8,sql%20FROM%20sqlite_schema--
    ```

### Bukti
<img width="1920" height="1080" alt="Screenshot (586)" src="https://github.com/user-attachments/assets/30be8693-d324-427e-81cf-ae5edf3cec04" />

<img width="1920" height="1080" alt="Screenshot (587)" src="https://github.com/user-attachments/assets/bb7d24f2-ceff-4ad0-8236-0803ec92f329" />

