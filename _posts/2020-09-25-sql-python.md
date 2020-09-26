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
File excel-nya bisa didownload di sini. Catatan, data sudah dimodifikasi untuk kepentingan pembelajaran semata. 

Dataset terdiri dari 22970 baris dan 5 kolom (```Sender ID```, ```Beneficiary ID```, ```Transaction Type```,  ```Merchant Name```, dan ```Invoice Number```).

```python
import pandas as pd
import numpy as np
import sqlite3 as sq

# untuk export excel ke db
from sqlalchemy import create_engine

# read excel, save to a dataframe
df = pd.read_excel('mtpg_report.xlsx',header=0, index_col=0)

# check shape
df.shape
````
