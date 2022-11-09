# Laporan Resmi Praktikum Jaringan Komputer Modul 2

## Anggota Kelompok
- 5025201248 - Fajar Zuhri Hadiyanto - pengerjaan bagian topologi, config ip, dan bagian proxy server
- 5025201047 - Sidrotul Munawaroh - pengerjaan dhcp server dan dhcp relay

## Layout
![00  topologi](https://user-images.githubusercontent.com/52820619/200829675-53cb00ee-570a-4b1e-a36f-4f10ddb4cf72.png)

## IP Address
### Ostania
![00  ip ostania](https://user-images.githubusercontent.com/52820619/200830180-507f68d2-9223-4537-aeb9-99072c355371.png)

### Wise
![00  ip wise](https://user-images.githubusercontent.com/52820619/200829753-dc557c97-2bf0-41ff-98a8-d42b58301ee4.png)

### Berlint
![00  ip berlint](https://user-images.githubusercontent.com/52820619/200829751-287f3960-9cea-4437-87b4-828c56a73448.png)

### Westalis
![00  ip westalis](https://user-images.githubusercontent.com/52820619/200829748-43d41136-fe85-450c-a4f3-f291599b0f98.png)

### SSS
![00  ip sss](https://user-images.githubusercontent.com/52820619/200829762-0d7fbf9d-bd87-42ce-8972-0a2d2d440475.png)

### Garden
![00  ip garden](https://user-images.githubusercontent.com/52820619/200829758-2f252b28-8945-4520-96a0-d69cbbe594d6.png)

### Eden
![00  ip eden](https://user-images.githubusercontent.com/52820619/200829741-3cc39baa-1cea-40ea-befb-2a1a61f9e3a7.png)

### Newstone Castle
![00  ip newstone castle](https://user-images.githubusercontent.com/52820619/200829738-ff55bceb-a1f3-4554-81b4-3c6e153d97dd.png)

### Kemono Park
![00  ip kemono park](https://user-images.githubusercontent.com/52820619/200829732-6c875d55-f8ef-4865-a706-3babe7af8c3f.png)

## Pengerjaan
### konfigurasi NAT Pada router
Pada node ostania yang berlaku sebagai router, jangan lupa untuk konfigurasi NAT dengan perintah `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.193.0.0/16`

### mengkonfigurasi dns pada node server
Pada node wise, berlint, dan westalis yang berlaku sebagai server, lakukan setting dns dengan buka file `/etc/resolv.conf`, lalu tambahkan isi sebagai berikut
```
nameserver 192.168.122.1
```

### Nomor 1
#### DNS Server di WISE
pada wise, lakukan instalasi package bind9 dengan perintah sebagai berikut
```
apt-get update
apt-get install bind9 -y
```

Setelah selesai melakukan instalasi, buka file `/etc/bind/named.conf.local`, isi sebagai berikut
```
zone "wise.e02.com" {
	type master;
	file "/etc/bind/wise/wise.e02.com";
};
```

Lalu, buat file `/etc/bind/wise/wise.e02.com`, isi sebagai berikut
```
$TTL    604800
@       IN      SOA     wise.e02.com. root.wise.e02.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@         IN      NS      wise.e02.com.
@         IN      A       192.193.2.2
@         IN      AAAA    ::1
```

Setelah itu, jangan lupa disave, dan restart bind9 dengan perintah `service bind9 restart`

#### DHCP Server di Westalis
Pada westalis, lakukan instalasi dhcp server dengan perintah
```
apt-get update
apt-get install isc-dhcp-server -y
```

#### Proxy Server di Berlint
Pada berlint, lakukan instalasi squid dengan perintah
```
apt-get update
apt-get install squid -y
```

lakukan konfigurasi dasar pada squid di file `/etc/squid/squid.conf` dengan isi sebagai berikut
```
http_port 8080
visible_hostname Berlint

http_access deny all
```

lakukan restart squid dengan perintah `service squid restart`

### Nomor 2
Ostania ditugaskan sebagai router sekaligus DHCP relay, lakukan instalasi dhcp relay dengan perintah 
```
apt-get update
apt-get install isc-dhcp-relay
```

lalu, lakukan konfigurasi dhcp relay pada file `/etc/default/isc-dhcp-relay` dengan isi sebagai berikut
```
SERVERS="192.193.2.4"
INTERFACES="eth1 eth3 eth2"
OPTIONS=""
```

lalu, restart dhcp relay dengan perintah `service isc-dhcp-relay restart`

### Nomor 3
client melalui switch 1 akan mendapatkan ip dari 192.193.1.50 hingga 192.193.1.88 dan 192.193.1.120 hingga 192.193.1.155.
Pada westalis, di file `/etc/dhcp/dhcpd.conf`, tambahkan konfigurasi berikut
```
subnet 192.193.1.0 netmask 255.255.255.0 {
    range 192.193.1.50 192.193.1.88;
    range 192.193.1.120 192.193.1.155;
    option routers 192.193.1.1;
    option broadcast-address 192.193.1.255;
    option domain-name-servers 192.193.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}

subnet 192.193.2.0 netmask 255.255.255.0 {
}
```

setelah itu, restart dhcp server dengan perintah `service isc-dhcp-server restart`

setelah itu, lakukan restart pada semua node client yang lewat switch 1

Berikut ini ip SSS

![03  ip sss](https://user-images.githubusercontent.com/52820619/200829727-ad5499f6-834b-4193-8a5c-e55a95f6bbfe.png)

Berikut ini ip Garden

![03  ip garden](https://user-images.githubusercontent.com/52820619/200829725-86b3f0cf-7552-43b7-96e5-69e3d0b909f6.png)

### Nomor 4
Hampir sama dengan nomor 3, client melalui switch 3 akan mendapatkan ip dari 192.193.3.10 hingga 192.193.3.30 dan 192.193.3.60 hingga 192.193.3.85.
Pada westalis, di file `/etc/dhcp/dhcpd.conf`, tambahkan konfigurasi berikut
```
subnet 192.193.3.0 netmask 255.255.255.0 {
    range 192.193.3.10 192.193.3.30;
    range 192.193.3.60 192.193.3.85;
    option routers 192.193.3.1;
    option broadcast-address 192.193.3.255;
    option domain-name-servers 192.193.2.2;
    default-lease-time 600;
    max-lease-time 6900;
}
```

setelah itu, restart dhcp server dengan perintah `service isc-dhcp-server restart`

setelah itu, lakukan restart pada semua node client yang lewat switch 3

Berikut ini ip NewstoneCastle

![04  ip newstone castle](https://user-images.githubusercontent.com/52820619/200829724-3270e8eb-f713-4786-b290-53eeff403b33.png)

Berikut ini ip KemonoPark

![04  ip kemono park](https://user-images.githubusercontent.com/52820619/200829721-85c708fa-ec41-4eff-9e89-2d980ea69c18.png)

### Nomor 5

Agar klien yang terhubung ke dns wise dapat terkoneksi ke internet, maka setting konfigurasi dns forwarder pada file `/etc/bind/named.conf.options` dengan isi sebagai berikut
```
options {
        directory "/var/cache/bind";
        forwarders {
                8.8.8.8;
                8.8.8.4;
        };
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

setelah itu, lakukan restart dns server dengan perintah `service bind9 restart`

### Nomor 6

Seperti yang sudah dipaparkan pada nomor 4 mengenai konfigurasi DHCP server, pada bagian
```
default-lease-time <time in seconds>;
max-lease-time <time in seconds>;
```
isi sesuai kebutuhan, yaitu 300 untuk default-lease-time dari subnet 1, 600 untuk default-lease-time dari subnet 3, dan 6900 untuk max-lease-time kedua subnet

### Nomor 7

Agar eden mendapatkan ip tetap, pertama, catat identifier dari port ethernet 0 pada eden dengan perintah `ip a`, lalu cek pada bagian link/ether seperti pada gambar berikut

![image](https://user-images.githubusercontent.com/52820619/200841155-3bc722b5-13d3-45b1-af57-c4403680597f.png)

lalu, seperti yang dilakukan di konfigurasi awal, tambahkan line `hwaddress ether <ethernet0 address>` pada bagian eth0

Setelah itu, pada westalis, di file `/etc/dhcp/dhcpd.conf`, tambahkan sebagai berikut
```
host Eden {
    hardware ethernet 9e:e5:3f:92:25:9d;
    fixed-address 192.193.3.13;
}
```

Setelah itu, restart dhcp server dengan perintah `service isc-dhcp-server restart`

Setelah itu, lakukan restart pada node eden

berikut ini merupakan ip dari eden

![07  ip eden](https://user-images.githubusercontent.com/52820619/200830188-0d1f312b-cad0-4203-9e8e-fdade53fd3a3.png)

### Proxy Server Rule 1 dan 2
client hanya bisa mengakses internet di luar jam kerja (senin-jumat, 08:00-17:00), dimana pada jam kerja, website yang bisa diakses hanya loid-work.com dan franky work .com.
Lakukan konfiguraasi, buat file `/etc/squid/acl.conf`, isi dengan 
```
acl WORKING_TIME time MTWHF 08:00-17:00
acl INTERNET_TIME_WEEKDEND time SA 00:00-23:59
acl INTERNET_TIME_WEEKDAYS time MTWHF 00:00-07:59
acl INTERNET_TIME_WEEKDAYS time MTWHF 17:01-23:59
acl WORKING_SITES dstdomain "/etc/squid/working-sites.acl"
```

lalu, tambahkan daftar whitelist site pada file `/etc/squid/working-sites.acl` dengan isi sebagai berikut
```
loid-work.com
franky-work.com
```

lalu, pada file `/etc/squid/squid.conf`, lakukan import pada file acl yang sudah dibuat tadi dengan menambahkan code `include /etc/squid/acl.conf` pada bagian atas file, lalu pada bagian access list, isi dengan 
```
http_access allow WORKING_SITES WORKING_TIME
http_access deny WORKING_SITES
http_access allow INTERNET_TIME_WEEKDEND
http_access allow INTERNET_TIME_WEEKDAYS
http_access deny WORKING_TIME
http_access deny all
```

lalu, restart squid dengan perintah `service squid restart`

berikut ini hasil testing pada client di weekend

![proxy rule 1 - weekend](https://user-images.githubusercontent.com/52820619/200829693-65b6ce19-f3bc-482f-af91-9d122bfb7c2b.png)

berikut ini hasil testing pada client di weekdays

![proxy rule 1 - weekdays](https://user-images.githubusercontent.com/52820619/200829697-580d429e-9367-47c5-a3ab-f12bcbb3ee0d.png)

berikut ini hasil testing pada client di working hour

![proxy rule 1 - working hour](https://user-images.githubusercontent.com/52820619/200829699-ddc1fb9c-38a9-47f6-b582-4cbd7e4cd64d.png)

### Proxy Server Rule 3

klien tidak dapat mengakses internet dengan protokol http, maka port 80 (port default http) harus diblokir, pada file `/etc/squid/acl.conf`, tambahkan isi sebagai berikut
```
acl HTTP_PORT port 80
```

lalu pada file `/etc/squid/squid.conf`, ubah bagian access list sehingga menjadi seperti berikut

```
http_access deny HTTP_PORT
http_access allow WORKING_SITES WORKING_TIME
http_access deny WORKING_SITES
http_access allow INTERNET_TIME_WEEKDEND
http_access allow INTERNET_TIME_WEEKDAYS
http_access deny WORKING_TIME
http_access deny all
```

Setelah itu, jangan lupa restart squid dengan perintah `service squid restart`

berikut ini hasil testing pada client

![proxy rule 3](https://user-images.githubusercontent.com/52820619/200829690-b1def4eb-2def-4590-b964-108e09b039e4.png)


### Proxy Server Rule 4 dan 5
akses internet dibatasi hingga 128kb/s pada waktu weekend. Tambahkan konfigurasi pada file `/etc/squid/squid.conf` setelah import dan sebelum access list sebagai berikut
```
delay_pools 1
delay_class 1 1
delay_access 1 allow INTERNET_TIME_WEEKDEND
delay_parameters 1 16000/16000
```

dimana INTERNET_TIME_WEEKEND sudah didefinisikan saat konfigurasi di praktik rule 1 dan 2, dan 16000 didapat dari 128kbps dibagi dengan 8 (128000/8 = 16000)

setelah itu, restart kembali dengan perintah `service squid restart`.

Agar lebih mudah dalam melakukan pengujian, allow semua access list pada squid.

Pada client, lakukan instalasi speedtest-cli dengan perintah sebagai berikut
```
apt-get update
apt-get install speedtest-cli -y
export http_proxy="http://192.193.2.3:8080"
export https_proxy="http://192.193.2.3:8080"
```

berikut ini merupakan hasil testing speedtest pada saat weekdays

![proxy rule 5 - weekdays](https://user-images.githubusercontent.com/52820619/200829687-c6242670-5a02-4816-946d-f80aa31fd79c.png)

berikut ini merupakan hasil testing speedtest pada saat weekend

![proxy rule 5 - weekends](https://user-images.githubusercontent.com/52820619/200829682-d792247c-504c-4f95-a5f0-2c2ab27ded4f.png)
