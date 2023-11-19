# **Praktikum 3 Jaringan Komputer**
<div align=justify>

Berikut adalah Repository dari Kelompok A17 untuk pengerjaan Praktikum Modul 3 Jaringan Komputer. Dalam Repository ini terdapat Anggota Kelompok, Dokumentasi serta Penjelasan tiap soal, Screenshot Output, dan Kendala yang dialami.

# **Anggota Kelompok**

| Nama                      | NRP        | Kelas                |
| ------------------------- | ---------- | ----------------     |
| Andrian                   | 5025211079 | Jaringan Komputer A  |
| Akmal Ariq Romadhon       | 5025211188 | Jaringan Komputer A  |


# **Grimoire (Laporan)**

Grimoire atau laporan mengenai hasil testing dari kelompok A17 dapat diakses melalui link [berikut](https://its.id/m/Jarkom-Kelompok-A17-Modul-3) atau melalui url berikut: https://its.id/m/Jarkom-Kelompok-A17-Modul-3. <br> 

Dalam grimoire tersebut terdapat perbandingan beberapa algoritma dan hasil testing saat client melakukan request tertentu.

# **Dokumentasi dan Penjelasan Soal**
<div align=justify>

Berikut adalah dokumentasi yang berisi source code dari tiap soal dan penjelasan terkait perintah yang digunakan. 

## **Soal Nomor 0 dan 1**
Setelah mengalahkan Demon King, perjalanan berlanjut. Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP mengarah pada worker yang memiliki IP [prefix IP].x.1. Lakukan konfigurasi sesuai dengan peta yang sudah diberikan. 

## **Penyelesaian Soal Nomor 0 dan 1**
![Nomor 12](https://media.discordapp.net/attachments/1150687865420906517/1175814014572429402/img1.png?ex=656c9922&is=655a2422&hm=b394308893e8ff51f2dafd92fbc1a796406470c916a700d072286bff7764fb47&=&width=883&height=701)

Konfigurasi Interface setiap node
```bash
#AURA

auto eth0
iface eth0 inet dhcp
up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.177.0.0/16


auto eth1
iface eth1 inet static
	address 192.177.1.0
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.177.2.0
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.177.3.0
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.177.4.0
	netmask 255.255.255.0


#HIMMEL
auto eth0
iface eth0 inet static
	address 192.177.1.1
	netmask 255.255.255.0
	gateway 192.177.1.0


#HEITER
auto eth0
iface eth0 inet static
	address 192.177.1.2
	netmask 255.255.255.0
        gateway 192.177.1.0


#DENKEN
auto eth0
iface eth0 inet static
	address 192.177.2.1
	netmask 255.255.255.0
	gateway 192.177.2.0

#EISEN
auto eth0
iface eth0 inet static
	address 192.177.2.2
	netmask 255.255.255.0
	gateway 192.177.2.0


## Untuk Worker dan Client menggunakan IP Dinamis
auto eth0
iface eth0 inet dhcp
hwaddress ether ea:5e:ce:cb:22: #Hanya untuk worker
```

Lalu, melakukan konfigurasi DNS seperti biasa pada DNS Server.

```bash
konfigurasi channel.a17.com
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     channel.a17.com. root.channel.a17.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      channel.a17.com.
@       IN      A       192.177.1.2
granz   IN      A       192.177.3.1 #IP PHP Worker 
@       IN      AAAA    ::1

```

```bash
konfigurasi canyon.a17.com
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     canyon.a17.com. root.canyon.a17.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;modu
@       IN      NS      canyon.a17.com.
@       IN      A       192.177.1.2 
riegel  IN      A       192.177.4.1 #IP Laravel Worker
@       IN      AAAA    ::1
```

Menkonfigurasi Domain pada named.local.conf
```
zone "canyon.a17.com" {
        type master;
        file "/etc/bind/jarkom/canyon.a17.com";
};

zone "channel.a17.com" {
        type master;
        file "/etc/bind/jarkom/channel.a17.com";
};
```
![Riegel Canyon](https://cdn.discordapp.com/attachments/1150687865420906517/1175816465849528360/image.png?ex=656c9b6b&is=655a266b&hm=aa4daca80f2efc05a56a0e04c2847e8188789fb2487dae5a4489bd37b8e23c43&)

![Riegel Canyon](https://cdn.discordapp.com/attachments/1150687865420906517/1175816508824363108/image.png?ex=656c9b75&is=655a2675&hm=64c15ce8b36cf594521e346d82a5a3649dffaf25a4e72ad414971097f37174b4&)

## **Soal Nomor 2**
Semua Client harus menggunakan konfigurasi dari DHCP Server. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80.


## **Penyelesaian Soal Nomor 2**
Melakukan Konfigurasi pada DHCP Relay dan DHCP Server

```bash
#DHCP RELAY UNTUK PERTAMA KALI

#Konfigurasi pada /etc/default/isc-dhcp-relay .
SERVERS="192.177.1.1"

INTERFACES="eth1 eth2 eth3 eth4"

#Konfigurasi /etc/sysctl.conf .
net.ipv4.ip_forward=1

#DHCP SERVER UNTUK PERTAMA KALI

#Konfigurasi pada file /etc/default/isc-dhcp-server.
INTERFACESv4="eth0"
INTERFACESv6=""

#Konfigurasi pada dhcpd.conf untuk subnet switch 3.
subnet 192.177.3.0 netmask 255.255.255.0 {
    range 192.177.3.16 192.177.3.32;
    range 192.177.3.64 192.177.3.80;
    option routers 192.177.3.0;
    option broadcast-address 192.177.3.255;
    option domain-name-servers 192.177.1.2;
    default-lease-time 180;
    max-lease-time 5760;
}
```


## **Soal Nomor 3**
Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168.

## **Penyelesaian Soal Nomor 3**
```bash
#Menambahkan konfigurasi pada file /etc/dhcp/dhcpd.conf pada DHCP SERVER .
subnet 192.177.4.0 netmask 255.255.255.0 {
    range 192.177.4.12 192.177.4.32;
    range 192.177.4.160 192.177.4.168;
    option routers 192.177.4.0;
    option broadcast-address 192.177.4.255;
    option domain-name-servers 192.177.1.2;
    default-lease-time 720;
    max-lease-time 5760;
}
```
Dapat dilihat bahwa terdapat pergantian IP pada client seperti pada gambar berikut:

![3](https://cdn.discordapp.com/attachments/1150687865420906517/1175817589889433630/image.png?ex=656c9c77&is=655a2777&hm=d88a47afda944bd3249cfd8a8d49ea0d1340e5175fded250a9b6254f8299e819&)

## **Soal Nomor 4**
Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut.

## **Penyelesaian Soal Nomor 4**
```bash
#Menambahkan forwarders pada file konfig /etc/bind/named.conf.options 

options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

      // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;
        listen-on-v6 { any; };
};
```

Untuk melakukan testing apakah _client_ sudah tersambung ke internet dapat dilakukan dengan ping google.com seperti pada gambar dibawah:

![4](https://cdn.discordapp.com/attachments/1150687865420906517/1175817951891431514/image.png?ex=656c9ccd&is=655a27cd&hm=1ef103290532bc1a8920beb2086c6aa725cc6ec9a61472a3ae5f500e2dac2b3f&)

## **Soal Nomor 5**
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit.

## **Penyelesaian Soal Nomor 5**
```bash
#Menambahkan konfigurasi ini pada subnet switch 3 
default-lease-time 180;
max-lease-time 5760;

#Menambahkan konfigurasi ini pada subnet switch 4
default-lease-time 720;
max-lease-time 5760;
```
Hasil DHCP

![5-1](https://media.discordapp.net/attachments/1150687865420906517/1175814013830037564/img4.png?ex=656c9922&is=655a2422&hm=473a96e5b4838ccf8dc6a671caecec1b8ccfea679cef0c8a1ec82d34bf74e634&=&width=986&height=320)

![5-2](https://media.discordapp.net/attachments/1150687865420906517/1175814014056542319/img3.png?ex=656c9922&is=655a2422&hm=6cc898dffb71339266d71a87c6440be192f12f239354e4b31f56e915355a514b&=&width=1037&height=330)


## **Soal Nomor 6**
Berjalannya waktu, petualang diminta untuk melakukan deployment. Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website [berikut](https://drive.google.com/file/d/1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1/view) dengan menggunakan php 7.3.

## **Penyelesaian Soal Nomor 6**  
Untuk menyelesaikan nomor 6, maka langkah pertama yang perlu dilakuka adalah melakukan instalasi untuk tools atau module yang diperlukan pada tiap worker dengan menjalankan file .sh berikut:

```bash
apt-get update

apt install nginx php php-fpm -y

apt-get install wget

apt-get install unzip

wget --no-check-certificate 'https://drive.usercontent.google.com/download?id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1&export=download&authuser=0&confirm=t&uuid=0e499712-8150-42d4-a474-b29dfb026ab6&at=APZUnTVBse4ducwDDntmAkLSWB1_:1699949521984' -O  granz.channel.a17.com

unzip granz.channel.a17.com

cp -r modul-3/ /var/www

rm -r modul-3

cp jarkom /etc/nginx/sites-available/

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx reload

service nginx restart

service php7.3-fpm start

service php7.3-fpm status
```
Dalam file .sh tersebut, dilakukan instalasi nginx, php-fpm versi 7.3 (default), wget, dan unzip. Kemudian dilakukan dowload file zip sesuai _website_ yang akan di_deploy_ pada tiap worker, dan diletakkan didalam root. File zip tersebut kemudian diunzip serta dipindahkan kedalam folder `/var/www`, agar dapat dikenali oleh PHP sebagai sebuah website. Terakhir, dilakukan _symbolic link_ antara `etc/nginx/sites-available` dan `/etc/nginx/sites-enabled` untuk folder php yang akan dideploy, serta penghapusan default folder agar tampilan _website_ saat dibuka tidak mengarah pada _landing page_ nginx. Setelah seluruh langkah tersebut dilakukan, maka nginx dan php akan direstart agar perunbahan yang dilakukan dapat dijalankan. Berikut adalah isi dari file kongfigurasi nginx:

```bash
server {
        listen 80;
        root /var/www/modul-3;

        index index.php index.html index.htm;
        server_name _;

        location /{
                try_files $uri $uri/ /index.php
                $query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        }

        location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
}
```

Berikut adalah output yang ditampilkan saat dilakukan lynx atau curl menuju alamat IP dari tiap-tiap worker: <br>

![Nomor6](https://cdn.discordapp.com/attachments/1150687865420906517/1175821090908545054/image.png?ex=656c9fb9&is=655a2ab9&hm=83a1b799011b450dc2178a1d910f5eb866c58db985192f19e6d916b53f90275f&)

## **Soal Nomor 7**
Kepala suku dari Bredt Region memberikan resource server sebagai berikut: <br>
- Lawine, 4GB, 2vCPU, dan 80 GB SSD.
- Linie, 2GB, 2vCPU, dan 50 GB SSD.
- Lugner 1GB, 1vCPU, dan 25 GB SSD. <br>

aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second.

## **Penyelesaian Soal Nomor 7** 
Untuk menyelesaikan soal nomor 7, hal yang harus dilakukan ialah menggunakan _load balancer_, sehingga saat terdapat request yang masuk akan terbagi kedalam 3 PHP Worker tersebut. Berikut adalah kongfigurasi yang digunakan untuk nginx:

```bash
upstream myweb  {
        server 192.177.3.1;
        server 192.177.3.2;
        server 192.177.3.3;
 }

 server {
        listen 80;
        server_name granz.channel.a17.com;

        location / {
        proxy_pass http://myweb;
        }
 }
``` 

Dalam code tersebut, digunakan tiga worker dengan ip 3.1, 3.2, dan 3.3. Kemudian server_name juga diatur menjadi granz.channel.com yang menggunakan port 80. Testing dapat dilakukan dengan menggunakan syntax `ab -n 1000 -c 100 http://granz.channel.a17.com` dengan hasil _testing_ sebagai berikut:

![Nomor7](https://cdn.discordapp.com/attachments/1150687865420906517/1174022556987183114/image.png?ex=656614b5&is=65539fb5&hm=eee8f7fae3bd4f150760117afcba49a9ae6f52b0fb5455a8ca34aa4db48d42b0&)

## **Soal Nomor 8**
Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut: <br>
- Nama Algoritma Load Balancer
- Report hasil testing pada Apache Benchmark
- Grafik request per second untuk masing masing algoritma. 
- Analisis.

## **Penyelesaian Soal Nomor 8**
Untuk melakukan _testing_  200 request dengan 10 _request per second_, digunakan command atau syntax `ab -n 200 -c 10 http://granz.channel.a17.com`. Syntax tersebut dijalankan setelah algoritma load balancer dirubah dengan tujuan untuk melihat algoritma mana yang tingkat efektivitasnya paling tinggi. Berikut adalah hasil tes dari kelompok A17:

- **Algoritma Round Robin**

![Nomor8RR](https://media.discordapp.net/attachments/1150687865420906517/1174024801145339986/image.png?ex=656616cc&is=6553a1cc&hm=4deba83768c4c45986f55939aabaa06b61418570ce133efac26f72ee0cf87eb2&=&width=608&height=701)

- **Algoritma _Least Connection_**

![Nomor8LC](https://media.discordapp.net/attachments/1150687865420906517/1174024892295938108/image.png?ex=656616e2&is=6553a1e2&hm=048c2b7b32aac8d6fb115fefd3b549809e6ae19b7274de47266976f6e8c8da49&=&width=653&height=701)

- **Algoritma _IP Hash_**

![Nomor8IH](https://media.discordapp.net/attachments/1150687865420906517/1174025137616588880/image.png?ex=6566171d&is=6553a21d&hm=2fdd32ec688f121d2291dea6c930234ecc78360e69aeff85dcf2e5b1fb246cd1&=&width=582&height=701)

- **Nomor 8 _Generic Hash_**

![Nomor8GH](https://media.discordapp.net/attachments/1150687865420906517/1174025958559338506/image.png?ex=656617e0&is=6553a2e0&hm=a452d392afd4bcf9c282a76f85c669aa31e8d56bf07ed11f1eb18bfad9e46017&=&width=605&height=700)

Untuk grafik serta penjelasan dari masing-masing algoritma dapat dilihat pada grimoire yang telah dibuat sebelumnya. 

## **Soal Nomor 9**
Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire.

## **Penyelesaian Soal Nomor 9** 
Untuk menyelesaikan nomor 9, hanya perlu dilakukan shutdown untuk 1 hingga 2 worker, kemudian dibandingkan grafik disetiap percobaannya. Berikut adalah hasil test dari kelompok kami dengan menggunakan syntax `ab -n 200 -c 10 http://granz.channel.a17.com`

- **Percobaan dengan 3 _Worker_**

![Nomor9-3Worker](https://cdn.discordapp.com/attachments/1150687865420906517/1174034776286572554/image.png?ex=65662017&is=6553ab17&hm=f2216db880094212157a322502617dce7511e23d497867e1a88679436437cd7c&)

- **Percobaan dengan 2 _Worker_**

![Nomor9-2Worker](https://media.discordapp.net/attachments/1150687865420906517/1174035129941897266/image.png?ex=6566206b&is=6553ab6b&hm=14bf04e2f3238c94bf8a8c077cad9ab58b049727cf7576e2d012f4de04555b43&=&width=522&height=583)

- **Percobaan dengan 1 _Worker_**

![Nomor9-1Worker](https://media.discordapp.net/attachments/1150687865420906517/1174035612328792114/image.png?ex=656620de&is=6553abde&hm=2685a9412ee31ef1a4cb67d91b7d56a2417fdb24d59d24e2033c48b2ac5c585f&=&width=560&height=585)

## **Soal Nomor 10**
Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/.

## **Penyelesaian Soal Nomor 10**
Untuk menyelesaikan nomor 10 hanya perlu dilakukan penambahan pada file kongfigurasi nginx, hingga menjadi seperti ini:
```bash
 server {
        listen 80;
        server_name granz.channel.a17.com;

        location / {
                proxy_pass http://myweb;
                auth_basic "Administrator's Area";
                auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
        }

        location ~ /\.ht {
                deny all;
        }
 }
```

Setelah melakukan kongfigurasi file nginx, maka dapat dijalankan command berikut untuk membuat direktori baru dan membuat file `.htpasswd`.

```bash
mkdir /etc/nginx/rahasiakita

htpasswd -b -c /etc/nginx/rahasiakita/.htpasswd netics ajka17
```
Setelah menjalankan command, tersebut, maka setiap user akan memerlukan username dan password untuk mengakses granz.channel.a17.com seperti pada gambar dibawah:

![Nomor11](https://cdn.discordapp.com/attachments/1150687865420906517/1174043354711982212/image.png?ex=65662814&is=6553b314&hm=3ffa0de6f19aa2717073aff85a438775e26c98e567e023c9d111bb4c803349c9&)

## **Soal Nomor 11**
Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id.

## **Penyelesaian Soal Nomor 11**
Untuk menambahkan proxy pass saat user mengakses `granz.channel.a17.com`, dapat ditambahkan bagian berikut pada kongfigurasi nginx:

```bash
       location /its {
                proxy_pass https://www.its.ac.id;

                allow all;
        }
```

Sehingga saat dijalankan akan menjadi seperti ini:

![Nomor11](https://cdn.discordapp.com/attachments/1150687865420906517/1174043493556047972/image.png?ex=65662835&is=6553b335&hm=2b4ead843cb4351f30e2e815b07a2fa6389597113eb5314ce1dff9a2f4f0b26b&)

## **Soal Nomor 12**
Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168.

## **Penyelesaian Soal Nomor 12**
Untuk menyelesaikan soal nomor 12, hal yang perlu dilakukan adalah menambahkan bagian berikut pada kongfigurasi nginx di load-balancer:

```bash
location / {
                proxy_pass http://myweb;
                auth_basic "Administrator's Area";
                auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;

                allow 192.177.3.69;
                allow 192.177.3.70;
                allow 192.177.4.167;
                allow 192.177.4.168;
                deny all;
        }
```

Sehingga outputnya akan menjadi seperti ini:

![Nomor12](https://cdn.discordapp.com/attachments/1150687865420906517/1175806795562876960/image.png?ex=656c9269&is=655a1d69&hm=31c24f46c2a7c52db546ac5d3326e3a41edd504c50c043705fd8b4019434c270&)

![Nomor13](https://cdn.discordapp.com/attachments/1150687865420906517/1175806857516953801/image.png?ex=656c9278&is=655a1d78&hm=4089a60c95f9d36460fa48379d6eef2be5e56fe1442da8fbed0f4a7847f58967&)

## **Soal Nomor 13**
Karena para petualang kehabisan uang, mereka kembali bekerja untuk mengatur riegel.canyon.yyy.com.
Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern.

## **Penyelesaian Soal Nomor 13**
Agar dapat diakses oleh worker, maka perlu dilakukan setup di database server. Berikut adalah file setup.sh yang digunakan untuk melakukan hal tersebut:

```bash
apt-get update

apt-get install mariadb-server -y

cp my.cnf /etc/mysql/

cp 50-server.cnf /etc/mysql/mariadb.conf.d/

service mysql start
```

Kemudian lakukan penambahan user didalam mysql. Untuk masuk pertama kali dapat menggunakan syntax `mysql` seperti gambar dibawah:

![Nomor13a](https://cdn.discordapp.com/attachments/1150687865420906517/1175807661737005076/image.png?ex=656c9338&is=655a1e38&hm=477ef5ff2d0166f8d4290f50d07d37181dea8b0e7c29e6012d70288e8c1f940f&)

Berikut adalah contoh bahwa database dapat diakses oleh worker:

![Nomor13](https://cdn.discordapp.com/attachments/1150687865420906517/1174329085049184326/image.png?ex=6567322f&is=6554bd2f&hm=0d9cf89f701e33df37e5171736ef7fd70769e504e3c425dcf27f2d532c9ee785&)
## **Soal Nomor 14**
Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer.

## **Penyelesaian Soal Nomor 14**
Untuk menyelesaikan soal nomor 14 maka perlu dilakukan instalas tools atau library yang diperlukan dengan file .sh berikut:

```bash
apt-get update

apt install nginx php php-fpm -y

apt-get install wget

apt-get install unzip

wget --no-check-certificate 'https://drive.usercontent.google.com/download?id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1&export=download&authuser=0&confirm=t&uuid=0e499712-8150-42d4-a474-b29dfb026ab6&at=APZUnTVBse4ducwDDntmAkLSWB1_:1699949521984' -O  riegel.canyon.a17.com

unzip riegel.canyon.a17.com

cp -r modul-3/ /var/www

rm -r modul-3

cp jarkom /etc/nginx/sites-available/

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx reload

service nginx restart

service php7.3-fpm start

service php7.3-fpm status
```

Setelah setup dijalankan, maka perlu dilakukan kongfigurasi untuk laravel, misalnya mengganti .env dari folder laravel. Berikut adalah contoh apabila laravel sudah sukses dijalankan:

![14a](https://cdn.discordapp.com/attachments/1150687865420906517/1175808483782828153/image.png?ex=656c93fb&is=655a1efb&hm=414edb10ce9031d430819dc19a6e4381ed2cbc571a78a56d1e7400c439fcdf63&)

Untuk testingnya dapat dilakukan dengan melakukan lynx ke salah satu worker seperti berikut:

![14](https://cdn.discordapp.com/attachments/1150687865420906517/1174329613242073139/image.png?ex=656732ad&is=6554bdad&hm=d46032c045b79c1a2cd2aa4f6545c7aab73372eb1d2e0788d5292cfeaea4e863&)

## **Soal Nomor 15**
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire untuk POST /auth/register.

## **Penyelesaian Soal Nomor 15**
Untuk soal nomor 15 dapat dilakukan dengan menembak endpoint ke beberapa worker. Untuk hasil testing dan perbandingan dapat dilihat pada grimoire, sedangkan untuk contoh response yang dihasilkan adalah sebagai berikut:

![15](https://cdn.discordapp.com/attachments/1150687865420906517/1175808999053066270/image.png?ex=656c9476&is=655a1f76&hm=80c7dd7af160a2eec52a19ccf2325320f7aa31e33ad21b4680acc9bcd16828c9&)

## **Soal Nomor 16**
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire untuk POST /auth/login.

## **Penyelesaian Soal Nomor 16** 

Untuk soal nomor 15 dapat dilakukan dengan menembak endpoint ke beberapa worker. Untuk hasil testing dan perbandingan dapat dilihat pada grimoire, sedangkan untuk contoh response yang dihasilkan adalah sebagai berikut:

![15](https://cdn.discordapp.com/attachments/1150687865420906517/1175808999053066270/image.png?ex=656c9476&is=655a1f76&hm=80c7dd7af160a2eec52a19ccf2325320f7aa31e33ad21b4680acc9bcd16828c9&)

## **Soal Nomor 17**
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire untuk GET /me.
## **Penyelesaian Soal Nomor 17**

Untuk soal nomor 15 dapat dilakukan dengan menembak endpoint ke beberapa worker. Untuk hasil testing dan perbandingan dapat dilihat pada grimoire, sedangkan untuk contoh response yang dihasilkan adalah sebagai berikut:

![17](https://cdn.discordapp.com/attachments/1150687865420906517/1175809357263425626/image.png?ex=656c94cc&is=655a1fcc&hm=d4abd821e137519730fe6599399421a48f9372b9761ed2cba6f41ab7128dc436&)

## **Soal Nomor 18**
Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern.

## **Penyelesaian Soal Nomor 18**

Untuk soal nomor 18, hal yang harus dilakukan untuk menyelesaikannya adalah dengan menambahkan load balancer dengan kongfigurasi seperti berikut:

```bash
upstream mynode  {
        least_conn;
        server 192.177.4.1:8001;
        server 192.177.4.2:8002;
        server 192.177.4.3:8003;
 }

 server {
        listen 80;
        server_name riegel.canyon.a17.com;

        location / {
        proxy_bind 192.177.2.2;
        proxy_pass http://mynode;
        }
 }
```

Kemudian untuk testing dapat dilakukan dengan melakukan lynx menuju IP dari load balancernya seperti pada gambar berikut:

![18](https://cdn.discordapp.com/attachments/1150687865420906517/1175810166093008996/image.png?ex=656c958d&is=655a208d&hm=23ed38f1555a3806552d1c4cde6f10bcb2afc61023119ca115dc444cdfff0bef&)

## **Soal Nomor 19**
Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers
sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.

## **Penyelesaian Soal Nomor 19** 
Untuk menyelesaikan soal nomor 19, maka dapat dibuat template didalam root folder. Kemudian pindahkan template tersebut kedalam `/etc/php/8.0/fpm/pool.d/www.conf` seperti pada gambar dibawah:

![19](https://cdn.discordapp.com/attachments/1150687865420906517/1175810509602291903/image.png?ex=656c95de&is=655a20de&hm=195911c8e6e3fc07db2f5660c30168f9de42be1e69694908ed58d589adeeabee&)

Kemudian lakukan hal yang sama di tiap worker, dan lakukan testing pada client. Untuk hasil lengkap percobaan dapat dilihat pada grimoire dari kelompok A17.

## **Soal Nomor 20**
Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second. 

## **Penyelesaian Soal Nomor 20**
Untuk menyelesaikan nomor 20, hanya perlu ditambahkan algoritma `least_conn;` pada kongfigurasi nginx di load-balancer untuk laravel seperti berikut

```bas
upstream mynode  {
        least_conn;
        server 192.177.4.1:8001;
        server 192.177.4.2:8002;
        server 192.177.4.3:8003;
 }

 server {
        listen 80;
        server_name riegel.canyon.a17.com;

        location / {
        proxy_bind 192.177.2.2;
        proxy_pass http://mynode;
        }
 }
```

Kemudian lakukan testing pada client. Untuk hasil lengkap testing dapat dilihat pada grimoire kelompok A17.

# **Kendala Saat Pengerjaan**

- Website DNS tidak bisa diakses dengan jaringan tertentu, seperti wifi kos. Hal tersebut menghambat pengerjaan karena koneksi dari hotspot tidak selalu .stabil.

- Terdapat beberapa soal yang ambigu, sehingga ada perubahan disaat sudah menyelesaikannya.

- Koneksi yang tiba-tiba terputus (_Closed by foreign host_) sehingga menghambat pengerjaan.

# **End of The Line**

```c
#include <stdio.h>

int main(){
    printf("Thank you!");
}
```
