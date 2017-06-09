---
layout: post
title: "Membuat Website Statis dengan Jekyll"
description: 
headline: 
modified: 2017-05-24
category: 
tags: 
imagefeature: 
mathjax: 
chart: 
comments: true
featured: true
---

Seperti dijelaskan di post sebelumnya, website ini dibuat dengan menggunakan [Jekyll](https://jekyllrb.com/), web hosting di Github Pages, dan nama domain dari [idwebhost](https://idwebhost.com/). Berikut langkah-langkah versi bahasa Indonesianya dari website [ini](http://www.mbaharsyah.xyz/web/2015/08/30/building-static-website/).

# Github Pages

Saya mendaftar di website Github dan membuat [User Pages](https://pages.github.com/) baru. Dimulai dengan membuat repository "`yanims.github.io`" dan menambahkan file "`index.html`" yang isinya "Hello, World!"

Selanjutnya tes page baru http://yanims.github.io di browser. "Hello, World" berhasil ditampilkan. Kemudian file dan repository tadi dihapus, buat repository yang baru lagi untuk real action.

# Jekyll

Saya menginstal Jekyll di Mac dan di Windows, karena di kantor pake Windows sementara di rumah pake Mac. 

Instalasi Jekyll memerlukan Ruby. Untuk Mac, prosesnya cukup cepat, tapi untuk Windows perlu langkah yang lebih banyak karena Jekyll for Windows tidak didukung secara resmi. Proses instalasi cukup mudah bahkan untuk saya yang tidak memiliki pengalaman dengan Ruby.

### Install Jekyll di Windows

Saya menggunakan Windows 7 Professional 64 bit. Langkah-langkah instalasi saya lihat [dari sini](http://jekyll-windows.juthilo.com/). 

Note: Jika sudah menginstall Ruby sebelumnya, skip langsung ke nomor 4.

1. Install Ruby, download dari [https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/). Saat muncul layar seperti berikut, cek "Add Ruby executables to your PATH". 

   ![rubysetup]({{ site.url }}/images/rubysetup.png)

2. Install Ruby Development Kit. Download dari tempat yang sama seperti tadi di [https://rubyinstaller.org/downloads](https://rubyinstaller.org/downloads)/, di bagian **Development Kit**. File ini ektensinya .exe tapi sebenarnya self-extracting archive. Ketika dieksekusi, user diminta untuk memasukkan path. Gunakan nama yang simple, misal `C:\RubyDevKit`. Klik "extract" dan tunggu hingga selesai.

3. Buka command line dan change directory ke tempat DevKit diekstrak.

   ```
   c:\ cd rubydevkit
   ```

   Auto-detect Ruby installations dan tambahkan ke file konfigurasi

   ```
   ruby dk.rb init
   ```

   Install DevKit, bind ke Ruby installation

   ```
   ruby dk.rb install
   ```

   Tadaaa...! Jika semua berjalan dengan baik, Ruby berhasil di install.

4. Install Jekyll

   ```
   gem install jekyll
   ```

   Selamat! Jekyll sudah terinstal.


### Install Jekyll di MacOS

Berikut ini langkah-langkah install Jekyll di macOS:

1. Install XCode command line tools. Jika sudah memiliki XCode sebelumnya, cukup menjalankan perintah berikut di terminal untuk menginstall XCode command line tools. Kemudian ikuti instruksi yang ada di layar.

   ```
   xcode-select --install
   ```

2. Install Jekyll menggunakan RubyGems dengan menggunakan command berikut di terminal:

   ```
   gem install jekyll
   ```

   Hal ini akan melakukan download dependencies, dan setelah semuanya selesai, Jekyll sudah siap untuk digunakan. 

## Membuat Website

Ada beberapa cara membuat website menggunakan Jekyll:

1. Menggunakan command line dengan perintah berikut:

   ```
   jekyll new blog
   ```

   Setelah selesai, folder `blog` akan dibuatkan di directory aktif saat ini. Perintah ini juga akan membuat struktur yang dibutuhkan untuk membangun website.

2. Menggunakan template. Ada banyak [template](http://jekyllthemes.org/) yang bisa didownload dan digunakan.

3. Menggunakan Fork and Clone dari sebuah Jekyll Github site/template. 


Untuk website ini saya menggunakan cara ke-2, mendownload template dari sebuah website yang menggunakan Jekyll Github site kemudian memodifikasinya sesuai kebutuhan.

## Update Website

Setelah membuat website dengan Jekyll, selanjutnya adalah memodifikasi file konfigurasi `_config.yml`. Biasanya berisi nama website, penulis, dan informasi lainnya sesuai template yang digunakan.

## Menjalankan Website dengan Server Lokal

Setelah selesai konfigurasi dan review post yang akan diupload, update, atau delete, website bisa dijalankan secara lokal di pc dengan menggunakan command berikut:

```
cd blog
jekyll serve .
```

​	Note: untuk command line di Windows tidak memerlukan tanda titik di ujung command.

Command ini akan melakukan *built* website. Setelah itu tampilan website bisa direview dari http://localhost:4000.

## Publish ke GitHub

Setelah website siap untuk dipublish ke GitHub pages, selanjutnya commit semua file ke dalam GitHub repository (yourusername.github.io). Jika pembuatan webiste menggunakan option 3, commit dengan command line bisa dilakukan dengan menjalankan perintah berikut:

```
git add --all
git commit -m "Publishing my website to Github"
git push origin master
```

Setelah itu file yang telah dimodifikasi akan diupload ke repository dan cek http://yourusername.github.io untuk melihat hasilnya.

Cara lain adalah dengan menggunakan aplikasi GitHub di [desktop](https://guides.github.com/introduction/getting-your-project-on-github/#desktop).  

# Custom Domain

Setelah semuanya berjalan dengan baik dan websitemu sudah bisa diakses dari http://yourusername.github.io, kamu bisa menghubungkannya dengan domain name pribadi. Saya menggunakan [idwebhost](https://idwebhost.com/), namun langkah-langkah berikut seharusnya cukup umum untuk host lainnya.

## Menambahkan CNAME file di Repository

Menggunakan langkah yang sama dengan index.html seperti di atas, buat sebuah nama bernama CNAME (semuanya huruf kapital) yang berisi sebaris tulisan yang merupakan nama domainmu. Contoh:

```
www.example.com
```

Cara lain adalah melalui setting dari GitHub repository.

![cnamesetting]({{ site.url }}\images\cnamesetting.PNG)

## Mengubah Setting DNS

Dari halaman control panel domain, cari DNS settings atau hosts records. Untuk idwebhost, terlebih dahulu perlu mengubah name server domain ke name server khusus untuk kelola DNS. Setting ini berada di`Domain > Kelola Domain > Nameserver`:

![nameserveridwebhost]({{ site.url }}\images\nameserveridwebhost.PNG)

Selanjutnya mengatur DNS setting yang berada di `Domain > Kelola Domain > Manage Nameservers > Alat pengelolaan > Kelola DNS`. Selanjutnya buat 2 records yang menunjuk ke alamat:

```
@ 192.30.252.154
@ 192.30.252.153
```

Secara umum ini berarti bahwa request ke domainmu akan dilanjutkan ke salah satu DNS tersebut (yang merupakan DNS GitHub). Setelah itu tugas dari GitHub adalah menunjuk ke website yang benar.

Kamu juga perlu menambahkan CNAME entry.

```
www yourusername.github.io
```

Yang berarti bahwa www. akan menunjuk ke yourusername.github.io. Kamu juga bisa mengubah www ke subdomain apapun yang kamu ingin miliki. Lihat gambar di bawah ini untuk set-up nama di idwebhost.

![idwebhostdns]({{ site.url }}\images\idwebhostdns.PNG)

Setelah itu kamu sudah siap, dan tunggu beberapa menit hingga setting DNS berlaku.