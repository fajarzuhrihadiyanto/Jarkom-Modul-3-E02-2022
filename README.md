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
