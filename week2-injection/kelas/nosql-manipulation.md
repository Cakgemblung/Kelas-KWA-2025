## Penyelesaian Tantangan: Manipulasi NoSQL

Tantangan ini memiliki tujuan untuk memperbarui seluruh ulasan produk secara serentak dengan memanfaatkan celah keamanan *NoSQL Injection*. Misi ini digolongkan sebagai tantangan injeksi dengan tingkat kesulitan 4 dari 6 bintang.

###  Permintaan Pembaruan Standar

 pertama adalah memahami bagaimana aplikasi memproses pembaruan ulasan produk yang normal.  tujuan ini,  menggunakan perangkat lunak **Burp Suite** yang berfungsi sebagai *proxy* untuk mencegat lalu lintas jaringan. Saya berhasil menangkap sebuah permintaan `PATCH` yang dikirimkan ke *endpoint* `/rest/products/reviews`. Permintaan ini membawa sebuah *body* berformat JSON yang berisi `id`

 ####  Permintaan yang Telah Direkayasa
 
 Struktur *body* yang telah dimodifikasi menjadi seperti berikut:

```json
{
  "id": {"$ne": null},
  "message": "All your reviews are belong to us!"
}
```

### Kesimpulan 

saya memuat ulang halaman produk pada peramban. Hasil ini menjadi bukti konkret bahwa serangan *NoSQL Injection* telah berhasil dijalankan dengan sukses.

### Bukti 
<img width="941" height="644" alt="image" src="https://github.com/user-attachments/assets/a3e3e55b-25b4-411e-a9fd-dce3d271c3d3" />
<img width="938" height="623" alt="image" src="https://github.com/user-attachments/assets/4ff05d02-cedc-4a03-9480-ec7f643d208e" />

