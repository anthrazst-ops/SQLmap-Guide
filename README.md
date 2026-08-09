# SQLmap

## Apa itu SQLmap?

SQLmap adalah tool otomatis untuk deteksi dan eksploitasi SQL Injection. Kasih URL, dia yang tes, ngedeteksi, dan narik datanya. Yang manual bisa berjam-jam, dia menit doang.

## Cara Kerja

SQLmap ngirim request ke target dengan berbagai payload SQL. Dia liat response server: beda, error, atau delay. Dari situ dia tau param mana yang rawan dan teknik injeksi apa yang kepake. Setelah ketemu, dia mulai narik data dari database.

## Empat Tangga

Belajar sqlmap cukup hafal 4 tangga, naik dikit-dikit dari tangga sebelumnya:

- Tangga 1, tes param rawan atau enggak.
- Tangga 2, minta daftar database.
- Tangga 3, pilih database, minta daftar tabel.
- Tangga 4, pilih tabel, dump isinya. Di sini flag biasanya keluar.

## Kapan Dipakai di CTF

- Ada param di URL, misal index.php?id=1
- Ada form login atau pencarian yang ngirim data ke server
- Response nunjukin error database, misal "SQL syntax error"
- Challenge kasih source code yang query-nya di-concatenate langsung

## Tentang --batch

Flag yang paling penting. SQLmap pas jalan suka nanya terus, misal "do you want to keep testing the others?" atau "do you want to exploit this SQL injection?". --batch jawab semua pertanyaan itu otomatis jadi iya. Tanpa --batch, command bakal nyangkut nunggu jawaban.

## Catatan Penting

- SQLite tidak mendukung --dbs, langsung lompat ke --tables
- Level default kadang gak kedetek param aneh di CTF, pake --level 3 --risk 2
- Kalau target butuh login, save request dari Burp dan pake -r
- Hanya untuk target lab atau CTF resmi

Command lengkap ada di [docs/basic-command.md](docs/basic-command.md)
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
