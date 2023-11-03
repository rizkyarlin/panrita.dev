---
title: SSL Error Ketika Menginstal Pycurl di MacOS
description: Penjelasan dan cara fix SSL Error Ketika Menginstal Pycurl di MacOS
# slug: hello-world
date: 2023-11-03 16:00:00+0800
# image: cover.jpg
categories:
    - Troubleshooting
tags:
    - python
    - macos
---

## Intro
Waktu saya beres-beres package di project python saya, saya mencoba mengupgrade `pycurl`. Jadi anggap saja saya sedang mencoba menjalan perintah `pip install pycurl` saya menemui error ini.

```python
ConfigurationError: Curl is configured to use SSL, but we have not been able to determine which SSL backend it is using. Please see PycURL documentation for how to specify the SSL backend manually.
```

## Apa yang terjadi?
PyCurl membutuhkan SSL untuk bisa diinstal. Sedangkan pada saat proses instalasi tadi, OS kita tidak tahu mau pakai Backend SSL apa. Pasti akan sangat bagus kalau misalnya bisa autoresolve yah.

## Cara memperbaiki
Setelah explore beberapa pertanyaan dan jawaban di StackOverflow, saya berhasil fix error ini dengan cara seperti ini:

1. Install openssl dengan
    ```bash
    brew install openssl
    ```
    Note: tanda $ menunjukkan kita lagi di terminal
2. Cari tau openssl terinstall dimana 
   ```bash
   $ brew --prefix openssl
   ``` 
   Outputnya akan terlihat seperti:
   ```
   $ /opt/homebrew/opt/openssl@3
   ```
   Output ini bisa jadi berbeda-beda berdasarkan versi OS dan cara teman-teman mengconfig homebrew
3. Output di step sebelumnya sebenarnya adalah shortcut. Yang ingin kita ketahui adalah lokasi asli dari openssl ini. Caranya dengan menggunakan perintah `ls -la` pada directory tadi. Lengkapnya 
    ```bash
    $ ls -la /opt/homebrew/opt/openssl@3
    ``` 
    Outputnya:
    ```bash
    $ lrwxr-xr-x  1 rizkyarlin  admin  25 Nov  2 14:09 /opt/homebrew/opt/openssl@3 -> ../Cellar/openssl@3/3.1.4
    ``` 
    
    Dari sini kita tahu kalau lokasi real dari openssl ada di `/opt/homebrew/Cellar/openssl@3/3.1.4`

    Output ini juga bisa berbeda dengan di Mac mu.

4. Nah sekarang setelah kita tau lokasi openssl-nya, kita sekarang akan memberitahu OS kita lokai openssl. Ada 3 variabel yang harus diset: `PYCURL_SSL_LIBRARY`, `LDFLAGS` dan `CPPFLAGS`.
	```bash
	export PYCURL_SSL_LIBRARY=openssl
	export LDFLAGS="-L/opt/homebrew/Cellar/openssl@3/3.1.4/lib"
	export CPPFLAGS="-I/opt/homebrew/Cellar/openssl@3/3.1.4/include"
    ```


5. Now sekarang kita sudah bisa coba lagi install `pycurl` nya
    ```bash
    pip install pycurl
    ```


Semoga membantu!