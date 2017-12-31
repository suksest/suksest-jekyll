---
layout: post
title:  "Writeup JuiceShop"
date:   2018-01-01 00:05:00 +0700
categories: writeup
---
# Score Board - 1 of 5  
> Find the carefully hidden 'Score Board' page.

Dalam permasalahan ini kita diminta untuk mencari halaman _scoreboard _ yang disembunyikan. Solusi dapat ditemukan ketika kita melihat _source_ dari halaman utama.

![Gambar 1](/home/sukma/Pictures/1-scoreboard.png)

Kemudian dengan mengganti _address bar_ dengan [`http://localhost:3000/#/score-board`](http://localhost:3000/#/score-board) kita dapat menemukan halaman _scoreboard_ yang 'tersembunyi'.

# Error Handling - 1 of 5
> Provoke an error that is not very gracefully handled.

Dalam permasalahan ini kita diminta untuk memprovokasi pesan _error_ yang tidak ditangani oleh aplikasi. Pesan _error_ dapat muncul jika kita memberikan input/mengakses halaman tertentu. Dalam kasus ini saya mencoba untuk memberikan _bad input_ dalam kolom **Search**.

_Bad input_ yang diberikan diantaranya:  
* `';` pada kolom **Search**  
Input ini memunculkan pesan _error_ pada _console_ seperti berikut:

![Gambar 2](/home/sukma/Pictures/2-badinput1.png)

* `'--` pada kolom **Search**  
Input ini memunculkan pesan _error_ pada _console_ seperti berikut:

![Gambar 3](/home/sukma/Pictures/2-badinput2.png)

# XSS Tier 1 - 1 of 5
> Perform a reflected XSS attack with `<script>alert("XSS1")</script>`.

Dalam permasalahan ini kita diminta untuk menemukan salah satu _vulnerability_ yaitu _reflected XSS_. Dalam kasus ini saya mencoba untuk memasukkan _code_ `<script>alert("XSS1")</script>` dalam kolom **Search**. _Browser_ akan merespon dengan menampilkan _alert_ seperti berikut:

![Gambar 4](/home/sukma/Pictures/3-xss1.png)

# Admin Section - 1 of 5
> Access the administration section of the store.

Permasalahan ini menunjukkan bahwa adanya halaman admin yang tidak terautentikasi. Solusi yang memungkinkan adalah kita melihat _source_ dari salah satu halaman pada aplikasi seperti pada gambar berikut:

![Gambar 5](/home/sukma/Pictures/4-admin-section.png)

Gambar tersebut menunjukkan bahwa ada URI tertentu yang mengakses halaman administator. Kemudian dengan mengganti _address bar_ dengan [http://localhost:3000/#/administration](http://localhost:3000/#/administration) kita dapat menemukan halaman _administration_ yang 'tersembunyi'.

Jika kita sebelumnya sudah terautentikasi sebagai user apapun, maka jika kita mengakses laman administator, laman ini akan menampilkan daftar username / email dari semua user yang telah terdaftar. Dari laman ini kita dapat mengetahui alamat email yang diduga merupakan alamat email administator yaitu `admin@juice-sh.op`.

# Zero Stars - 1 of 5
> Give a devastating zero-star feedback to the store.

Pada permasalahan ini kita diminta untuk memberikan _feedback_ ke toko dengan rating 0 (ditandai dengan 0 bintang). Kemungkinan _vulnerability_ adalah pada saat proses input rating, baik secara langsung atau melewati API. Pada kasus ini ditemukan _vulnerability_ pada proses input. Pertama kita masukan _feedback_ dengan rating 5 **tanpa me-klik tombol submit** seperti pada gambar berikut:

![Gambar 6](/home/sukma/Pictures/6-zerostar.png)

Kemudian ubah rating menjadi 0 kemudian klik tombol submit seperti pada gambar berikut:

![Gambar 7](/home/sukma/Pictures/6-zerostar2.png)

# Five-Star Feedback - 1 of 5
> Get rid of all 5-star customer feedback.

Pada permasalahan kali ini kita diminta untuk menghapus _feedback_ dengan 5 bintang. Hal ini dapat dilakukan oleh user apapun yang telah _Log In_. Seperti pada gambar berikut:

![Gambar 8](/home/sukma/Pictures/7-5star.png)

# Confidential Document
> Access a confidential document.

Pada permasalahan kali ini kita diminta untuk mengakses dokumen penting toko tersebut. Setelah mencoba untuk mengakses semua fitur, ternyata ketika **Checkout** pada keranjang belanja, kita diberikan invoice pemesanan dengan format alamat `localhost:3000/ftp/<orderid>`. Sekarang kita coba akses http://localhost:3000/ftp. _Browser_ akan menampilkan file yang ada dalam suatu direktori. Dokumen penting yang dimaksud adalah file `acquisitions.md`.

# Login Admin - 2 of 5
> Log in with the administrator's user account.

Permasalahan ini menunjukkan bahwa adanya kelemahan pada account administator atau mekanisme _Log In_ dari aplikasi tersebut.

## Solusi 1 - _SQL Injection_
Pertama kita lakukan _bad input_ `'\\`, kemudian menghasilkan _error_ seperti berikut:

![Gambar 9](/home/sukma/Pictures/5-sqlierror.png)

Dari pesan _error_ tersebut kita dapat mengetahui _query_ untuk _Log In_. Dengan menggunakan _input_ `' OR 1=1--` atau `admin@juice-sh.op--` kita dapat melakukan _Log In_.

## Solusi 2 - _Weak Password_
Cara ini akan dijelaskan pada bagian [Password Strength](#password-strength)

# Password Strength - 2 of 5
>  Log in with the administrator's user credentials without previously changing them or applying SQL Injection.

Pada permasalahan ini kita diminta untuk _Log In_ dengan menggunakan _account_ administrator tanpa menggunakan _SQL Injection_. Menurut laman [berikut](https://www.csoonline.com/article/2226175/microsoft-subnet/microsoft-subnet-top-25-most-commonly-used-and-worst-passwords-of-2013.html) ada beberapa kemungkinan password yang lemah. Pertama saya mencoba password `admin` dengan alamat email yang didapatkan dari laman administator, tetapi salah. Dalam [artikel tentang kelemahan password](https://www.csoonline.com/article/2226175/microsoft-subnet/microsoft-subnet-top-25-most-commonly-used-and-worst-passwords-of-2013.html) disebutkan bahwa diantara password yang lemah adalah penambahan _suffix_ `123`. Ketika dicoba dengan `admin123`, _Log In_ berhasil. Solusi dalam permasalahan ini juga dapat menyelesaikan permasalahan [Login Admin](#log-in-admin)

# Basket Access - 2 of 5
> Access someone else's basket.

Pada permasalahan ini kita diminta untuk mengakses keranjang orang lain. Setelah ditelusuri aplikasi menyimpan variabel `bid` seperti pada gambar berikut:

![Gambar 10](/home/sukma/Pictures/8-bid.png)

Ketika kita mengganti **Value** pada **Key** `bid` maka kita dapat mengakses keranjang akun lain.

# Forgotten Sales Backup - 2 of 5
> Access a salesman's forgotten backup file.

Pada permasalahan ini kita diminta untuk mengakses file _backup_ dari penjualan. Pada umumnya file _backup_ memiliki ekstensi `.bak`. Pada halaman http://localhost/ftp terdapat file `coupons_2013.md.bak`. Ketika file tersebut dibuka akan tampil pesan _error_ seperti berikut:

![Gambar 11](/home/sukma/Pictures/9-coupons-error.png)

Namun, jika kita membuka file lain yang berekstensi `.md` maka akan muncul parameter `?md_debug=true`. Tetapi cara ini tidak berhasil jika kita menambahkan parameter tersebut ketika membuka file `coupons_2013.md.bak`. Ketika nilai dari parameter tersebut diganti menjadi `.md` maka file berhasil di download.

# Christmas Special - 2 of 5
> Order the Christmas special offer of 2014.

Pada permasalahan ini kita diminta untuk mengakses produk spesial Hari Natal tahun 2014. Halaman utama tidak menampilkan produk yang dimaksud, begitupun jika mencari produk tersebut. Karena fitur **Search** memiliki kelemahan _SQL Injection_ maka kita coba berikan _bad input_ `';`. Kita akan mendapatkan pesan _error_ seperti berikut:

![Gambar 12](/home/sukma/Pictures/10-deletedAt.png)

Dalam _query_ tersebut terdapat `WHERE clause` yaitu `deletedAt`. Sekarang _input_ `'--` pada kolom **Search**, maka akan ada satu produk spesial Hari Natal tahun 2014.

# Login Jim - 3 of 5
> Log in with Jim's user account.

Pada permasalahan ini kita diminta untuk _Log in_ sebagai Jim. Kita dapat melakukan _SQL Injection_ seperti pada permasalahan lainnya. Pada kolom email masukkan `' OR email LIKE '%jim%'--` dan sembarang pada kolom password. Maka kita akan _Log In_ sebagai Jim.

# Login Bender - 3 of 5
> Log in with Bender's user account.

Pada permasalahan ini kita diminta untuk _Log in_ sebagai Bender. Kita dapat melakukan _SQL Injection_ seperti pada permasalahan lainnya. Pada kolom email masukkan `' OR email LIKE '%bender%'--` dan sembarang pada kolom password. Maka kita akan _Log In_ sebagai Bender.

# Forgotten Developer Backup 3 of 5
> Access a developer's forgotten backup file.

Pada permasalahan ini kita diminta untuk mengakses file _backup_ dari developer. Pada umumnya file _backup_ memiliki ekstensi `.bak`. Pada halaman http://localhost/ftp terdapat file yang mungkin yaitu `package.json.bak`. Ketika file tersebut dibuka akan tampil pesan _error_ seperti berikut:

![Gambar 11](/home/sukma/Pictures/9-coupons-error.png)

Namun, jika kita membuka file lain yang berekstensi `.md` maka akan muncul parameter `?md_debug=true`. Tetapi cara ini tidak berhasil jika kita menambahkan parameter tersebut ketika membuka file `coupons_2013.md.bak`. Ketika nilai dari parameter tersebut diganti menjadi `.md` file tersebut tetap tidak dapat dibuka. Jika kita menambahkan **_Null Byte_** pada ekstensi link tersebut maka file tersebut dapat didownload. **_Null Byte_** yang dimaksud adalah seperti berikut http://localhost:3000/ftp/package.json.bak%2500.md
