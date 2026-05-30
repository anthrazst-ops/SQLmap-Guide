# SQLmap

## Apa itu SQLmap?

SQLmap adalah tool open-source untuk otomasi deteksi dan eksploitasi celah **SQL Injection** pada aplikasi web. Tool ini ditulis dalam Python dan tersedia secara gratis di [sqlmap.org](https://sqlmap.org).

Singkatnya — SQLmap melakukan pekerjaan yang kalau dilakukan manual bisa memakan waktu berjam-jam, hanya dalam hitungan detik.

---

## Definisi SQL Injection

SQL Injection adalah celah keamanan di mana attacker bisa **menyisipkan perintah SQL** ke dalam input yang dikirim ke database. Kalau aplikasi tidak memvalidasi input dengan benar, database akan mengeksekusi perintah tersebut.

Contoh sederhana:

Query normal di belakang layar:
```sql
SELECT * FROM users WHERE username = 'admin' AND password = '1234'
```

Kalau input tidak divalidasi, attacker bisa kirim:
```
username: admin'--
```

Query menjadi:
```sql
SELECT * FROM users WHERE username = 'admin'--' AND password = '...'
```

Tanda `--` adalah komentar di SQL — artinya bagian password diabaikan, dan attacker bisa login tanpa password.

---

## Cara Kerja SQLmap

SQLmap bekerja dengan cara:

1. **Mengirim request** ke target URL atau form yang ditentukan
2. **Menyisipkan payload** SQL di setiap parameter yang ditemukan
3. **Menganalisis response** dari server — apakah ada perbedaan, error, atau delay
4. **Menentukan teknik injeksi** yang berhasil dari beberapa teknik yang tersedia:
   - Boolean-based blind
   - Time-based blind
   - Error-based
   - UNION query
   - Stacked queries
5. **Mengeksploitasi celah** tersebut untuk mengekstrak data dari database

---

## Kegunaan SQLmap

- Mengekstrak isi database (username, password, data sensitif)
- Mengetahui struktur database (nama database, tabel, kolom)
- Bypass autentikasi login
- Dalam konteks CTF: menemukan flag yang tersimpan di dalam database

---

## Digunakan Saat User Melihat Apa?

SQLmap relevan digunakan ketika kamu melihat hal-hal berikut di sebuah website:

**Di URL:**
```
http://target.com/index.php?id=1
http://target.com/product?category=electronics&sort=price
http://target.com/user?name=john
```
Tanda tanya `?` diikuti `parameter=value` adalah sinyal utama.

**Di form:**
- Form login (username & password)
- Form pencarian
- Form komentar atau input apapun yang mengirim data ke server

**Di response:**
- Muncul error database: `MySQL syntax error`, `Warning: mysql_fetch`
- Halaman berbeda saat input diubah sedikit
- Response lambat saat input tertentu dikirim (indikasi time-based injection)

---

## Logika di Baliknya

Konsep dasarnya adalah **kepercayaan buta terhadap input user**.

Aplikasi web yang rentan langsung memasukkan input user ke dalam query SQL tanpa validasi:

```python
# Kode berbahaya (rentan)
query = "SELECT * FROM users WHERE id = " + user_input

# Kode aman (menggunakan parameterized query)
query = "SELECT * FROM users WHERE id = ?"
cursor.execute(query, (user_input,))
```

SQLmap mengeksploitasi pola pertama — ia mencoba berbagai payload untuk "memecah" struktur query dan menyisipkan perintahnya sendiri.

Kalau server merespons berbeda terhadap payload tertentu, SQLmap tahu bahwa titik tersebut vulnerable dan mulai mengekstrak data secara sistematis.

---

## Catatan Penting

SQLmap hanya boleh digunakan pada:
- Sistem yang kamu miliki sendiri
- Lab atau environment yang dirancang untuk latihan (DVWA, HackTheBox, TryHackMe)
- Target yang sudah mendapat izin eksplisit secara tertulis

Penggunaan tanpa izin adalah ilegal.
