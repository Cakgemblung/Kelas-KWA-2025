# Juice-Shop Write-up: Imaginary Challenge

## 1. Tantangan

- **Judul:** Imaginary Challenge
- **Kategori:** Cryptographic Issues
- **Tingkat Kesulitan:** ⭐⭐⭐⭐⭐⭐ (6 dari 6)

Tantangan "Imaginary Challenge" di OWASP Juice Shop adalah salah satu yang paling rumit karena menuntut pemahaman mendalam tentang **mekanisme internal aplikasi**. Tujuannya adalah untuk "menyelesaikan" tantangan nomor 999 yang tidak ada dengan merekayasa balik **"Continue Code"** yang valid.

---

## 2. Metodologi dan Solusi

Pendekatan untuk tantangan ini adalah **analisis kode sumber** (white-box testing) karena metode konvensional (black-box testing) tidak akan berhasil mengungkap parameter rahasia yang digunakan.

### Langkah 1: Analisis Kode Sumber (Kunci Solusi)

Semua upaya untuk membuat hash yang valid secara eksternal gagal karena parameter yang digunakan tidak cocok dengan yang ada di server. Dengan menganalisis kode sumber aplikasi, parameter rahasia yang digunakan oleh library `hashids` untuk membuat "Continue Code" berhasil ditemukan:

* **`salt`**: `'this is my salt'`
* **`minLength`**: `60`
* **`alphabet`**: `'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890'`

Parameter unik inilah yang membuat tantangan ini mustahil diselesaikan tanpa melihat "ke dalam" aplikasi.

### Langkah 2: Pembuatan `Continue Code` yang Benar

Dengan parameter yang telah ditemukan, sebuah skrip JavaScript dibuat untuk menghasilkan `Continue Code` yang valid untuk tantangan fiktif nomor `999`. Skrip ini dijalankan langsung di **Console browser** untuk memastikan lingkungan eksekusinya sama.

```javascript
// Definisikan parameter yang ditemukan dari analisis kode sumber
const salt = 'this is my salt';
const minLength = 60;
const customAlphabet = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890';

// Muat pustaka hashids dari CDN dan jalankan fungsi utama
const script = document.createElement('script');
script.src = '[https://cdnjs.cloudflare.com/ajax/libs/hashids/2.2.10/hashids.min.js](https://cdnjs.cloudflare.com/ajax/libs/hashids/2.2.10/hashids.min.js)';
script.onload = () => {
    // Buat hash dengan parameter yang benar
    const hashids = new Hashids(salt, minLength, customAlphabet);
    const continueCode = hashids.encode(999);
    console.log('Final Continue Code yang Benar adalah:', continueCode);
    window.generatedCode = continueCode; // Simpan untuk digunakan di langkah berikutnya
};
document.head.appendChild(script);
```

### Bukti

<img width="1920" height="1080" alt="Screenshot (81)" src="https://github.com/user-attachments/assets/d98ddc75-4ba7-4e41-b532-f0c020a85ba7" />
<img width="1920" height="1080" alt="Screenshot (82)" src="https://github.com/user-attachments/assets/b9323a65-c662-4f05-8801-841b240ed360" />
