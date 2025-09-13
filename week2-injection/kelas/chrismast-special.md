##  Tantangan: Christmas Offer

Tantangan ini memiliki tingkat kesulitan menengah ke atas, yakni 4 dari 6 bintang, yang bertujuan untuk menemukan dan membeli sebuah produk rahasia bernama "Christmas Offer".

### Langkah Eksploitasi 
Produk "Christmas Offer" sengaja disembunyikan dengan cara memberinya status "telah terhapus" pada basis data (kolom `deletedAt` diisi dengan sebuah nilai). 

*   **URL Target Injeksi:** Celah keamanan ini ditemukan pada parameter `q` di alamat `http://127.0.0.1:3000/rest/products/search?q=`
*   **Kode Injeksi:** Saya menggunakan kode `')) UNION SELECT * FROM Products WHERE deletedAt IS NOT NULL--` yang dimasukkan ke dalam parameter `q`. Kode ini secara khusus dirancang untuk memanipulasi kueri basis data agar mengembalikan semua produk yang telah ditandai sebagai terhapus, sambil mengabaikan sisa kueri asli.
*   **Hasil Eksekusi:** Setelah menjalankan URL dengan kode injeksi tersebut pada peramban, saya berhasil memperoleh data mentah dalam format JSON yang berisi semua produk tersembunyi. Dari data tersebut, saya menemukan produk "Christmas Surprise Box" dengan `ProductId` bernilai 10.

#### Eksekusi dan Informasi Kunci:
*   **ProductId:** Telah didapatkan sebelumnya, yaitu `10`.
*   **BasketId:** ID keranjang belanja dapat ditemukan dengan memeriksa `Developer Tools > Application > Session Storage` pada peramban.
*   **Token Otorisasi:** Untuk proses autentikasi permintaan ke API, token otorisasi diambil dari `Developer Tools > Application > Local Storage`.

Saya memanfaatkan fitur `fetch` yang tersedia di *Console* peramban untuk mengirimkan sebuah permintaan `POST` ke *endpoint* `/api/BasketItems/`. Permintaan ini menyertakan sebuah *payload* JSON yang berisi seluruh informasi yang telah disiapkan.

```javascript
fetch('/api/BasketItems/', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer <token_anda_di_sini>'
    },
    body: JSON.stringify({
        ProductId: 10,
        BasketId: "3",
        quantity: 1
    })
});
```

### Kesimpulan 

Permintaan API tersebut berhasil dieksekusi, yang menyebabkan produk "Christmas Surprise Box" langsung ditambahkan ke dalam keranjang belanja. 

### Bukti
<img width="1260" height="424" alt="image" src="https://github.com/user-attachments/assets/fb1a0522-aeab-488b-90b0-cb5bf367322a" />
<img width="1223" height="553" alt="image" src="https://github.com/user-attachments/assets/c8071076-cc6a-4c46-8dab-0e556a7ff2b5" />
<img width="717" height="563" alt="image" src="https://github.com/user-attachments/assets/8a2cfd34-1c6c-4f12-b8d2-24c41a279f67" />
