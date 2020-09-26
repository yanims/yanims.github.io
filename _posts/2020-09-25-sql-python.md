---
layout: post
title: "SQLite Query di Python"
description: 
headline: 
modified: 2020-09-25
category: 
tags: 
imagefeature: 
mathjax: 
chart: 
comments: true
featured: true
---

tldr; membuat sql query di python dengan datasource dari sebuah file excel.

Ceritanya tadi ada task untuk mengolah data sebuah file excel berisi 22970 baris data transfer-transferan. Inti tugasnya adalah menampilkan seluruh pengirim (```Sender ID```), penerima (```Beneficiary ID```), dan transaksi (```Invoice Number```) yang duplicated.

Sebenarnya gampang, ya, tinggal klik-klik langsung filter di excel. Tapi data sebanyak 22970 ini cukup untuk membuat komputer gw ngos-ngosan. 

Terus kepikiran pengen buat macro di VBA Excel, nanti diquery pakai sql buat filternya. Tapi udah lupa cara bikin macro. Udah aja 1 jam berkutat belajar lagi macro terus menyerah pas gagal bikin koneksinya pakai ADODB (gak bisa create object kalau pakai macOS, ceunah). 

Tiba-tiba. Oh, kenapa gak pakai Python aja kalau mau diperlakukan sebagai sql? Kayaknya lebih cepat. Tapi sebenarnya gak cepat juga, karena gw pengennya data itu jadi tabel dalam sebuah db yang nantinya akan gw query dengan sql (entah mengapa ngotot banget pengen pake sql query). Sementara gw belum tau caranya convert excel ke db. Jadilah lama lagi nyari-nyari caranya. Tambah lagi udah buang waktu buat mencoba bikin macro. Kayaknya lebih cepat kalau bikin filter langsung di excel dari tadi, wkwkwk.

# Data
File excel-nya bisa didownload di [sini](https://github.com/yanims/PYTN/blob/master/Data/mtpg_report.xlsx). Catatan, data sudah dimodifikasi, baik isi maupun jumlah field, untuk kepentingan pembelajaran semata. 

Dataset terdiri dari 22970 baris dan 5 kolom (```Invoice Number```, ```Transaction Type```,  ```Merchant Name```, ```Sender ID```, dan ```Beneficiary ID```).

Pertama, seperti biasa import dulu library yang akan digunakan. ```Pandas``` untuk membuat DataFrame, ```sqlite3``` untuk query sql, dan ```sqlalchemy``` untuk mengexport data dari excel ke db.

```python
import pandas as pd
import sqlite3 as sq

# untuk export excel ke db
from sqlalchemy import create_engine
```

Kemudian, baca excel dan simpan di DataFame ```df```. Terus check shape hasilnya ```(22907,4)```.
```python
# read excel, save to a dataframe
df = pd.read_excel('mtpg_report.xlsx',header=0, index_col=0)

# check shape
df.shape
````

# Export dataframe ke DB

Pertama kita siapkan koneksi dulu. Database yang akan dibuat bernama ```mtpg_report.db```. 
```python
# preparing connection to db
engine = create_engine('sqlite:///mtpg_report.db', echo=True)
sqlite_connection = engine.connect()
```
Setelah itu export Dataframe ke tabel bernama ```mtpg```. Di sini kita juga melakukan pengecekan, jika tabelnya sudah ada maka direplace. Bisa juga diganti dengan ```fail``` kalau tidak mau ```replace``` dengan db yang baru. Cek dokumentasinya di [sini](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_sql.html).
```python
# export df to sqlite db, if exists then replace
sqlite_table = "mtpg"
df.to_sql(sqlite_table, sqlite_connection, if_exists='replace')
```
Proses di atas mengexport seluruh data dalam dataframe ke sebuah tabel. Jadi tidak perlu lagi mengcopy datanya satu-persatu. Setelah export selesai, tutup koneksinya.
```python
sqlite_connection.close()
```
# Query menggunakan SQL
Kita coba query sederhana dulu dengan memilih 5 record pertama. Inget, koneksinya ke db tapi pas select tetap ke tabel.
```python
# check creted db by selecting 5 records
conn = sq.connect("mtpg_report.db")
df_mtpg = pd.read_sql_query ("select * from mtpg limit 5;", conn)
df_mtpg
```
Jika tidak ada masalah maka outputnya adalah 5 baris pertama dari data. Kalau sudah OK, kita bisa mulai query yang sesungguhnya.

Yang pertama adalah me-list semua ```Sender ID``` yang duplicated. Misal kita ingin mencari ```Sender ID``` yang duplicated dari ```Merchant Name = WAL``` dan ```Transaction Type = Transfer OUT```. Untuk menampilkan semua ```Sender ID``` yang duplicated, bisa dilakukan dengan ```GROUP BY``` diikuti dengan select ```COUNT``` yang lebih dari 1.

```python
#  sender id duplicated
str_query = "SELECT * \
            FROM mtpg \
                WHERE (\"Sender ID\") IN \
                (SELECT \"Sender ID\" \
                    FROM mtpg WHERE \"Merchant Name\" = \"WAL\" \
                    AND \"Transaction Type\" = \"Transfer OUT\" \
                    GROUP BY \"Sender ID\" \
                    HAVING COUNT (*)>1 \
                    )\
            ORDER BY \"Sender ID\" DESC"
df_mtpg = pd.read_sql_query (str_query, conn)
df_mtpg
```
Contoh ke dua masih seperti yang pertama, tapi kali ini kita tambahkan kriterianya. Selain ```Sender ID``` yang duplicated, kita juga ingin melist ```Beneficiary ID``` yang duplicated. 
```python
# beneficiary id, dan sender id duplicated
str_query = "SELECT * \
            FROM mtpg \
                WHERE (\"Beneficiary ID\") IN \
                (SELECT \"Beneficiary ID\" \
                 FROM mtpg WHERE \"Sender ID\" IN  \
                    (SELECT \"Sender ID\" \
                        FROM mtpg WHERE \"Merchant Name\" = \"WAL\" \
                        AND \"Transaction Type\" = \"Transfer OUT\" \
                        GROUP BY \"Sender ID\" \
                        HAVING COUNT (*)>1 \
                    )\
                GROUP BY \"Beneficiary ID\" \
                HAVING COUNT (*)>1 \
                )\
            ORDER BY \"Sender ID\" DESC"
df_mtpg = pd.read_sql_query (str_query, conn)
df_mtpg
```
Contoh ke-tiga, tambah lagi dengan kriteria ```Invoice Number``` yang duplicated. 
```python
# invoice no, beneficiary id, dan sender id duplicated

str_query = "SELECT * \
            FROM mtpg \
                WHERE (\"Invoice Number\") IN \
                (SELECT \"Invoice Number\" \
                    FROM mtpg WHERE \"Beneficiary ID\" IN \
                    (SELECT \"Beneficiary ID\" \
                     FROM mtpg WHERE \"Sender ID\" IN  \
                        (SELECT \"Sender ID\" \
                            FROM mtpg WHERE \"Merchant Name\" = \"WAL\" \
                            AND \"Transaction Type\" = \"Transfer OUT\" \
                            GROUP BY \"Sender ID\" \
                            HAVING COUNT (*)>1 \
                        )\
                    GROUP BY \"Beneficiary ID\" \
                    HAVING COUNT (*)>1 \
                    )\
                GROUP BY \"Invoice Number\" \
                HAVING COUNT (*)>1) \
                ORDER BY \"Sender ID\" DESC"
df_mtpg = pd.read_sql_query (str_query, conn)
df_mtpg
```
Terakhir, simpan hasilnya di file excel.
```python
# save result to an excel file
df_mtpg.to_excel("mtpg_filter_duplicated.xlsx")
```

That's all. Kalau ada saran untuk meringkas query-nya, boleh di komen, hehe. Jupyter notebook untuk latihan ini bisa didownload di [sini](https://github.com/yanims/PYTN/blob/master/mtpg_filter.ipynb). Thank you. 

