# Jarkom-Modul-5-B06-2022

| **No** | **NRP** | **Nama** | 
| ------------- | ------------- | --------- |
| 1 | 5025201100 | Hansen Idden | 
| 2 | 5025201109 | Mohammad Firman Fardiansyah |
| 3 | 5025201167 | William Zefanya Maranatha |

```PREFIX IP = 10.6```

## Topologi

## Pembagian Subnet

## Pembagian IP
| SUBNET |	JUMLAH IP |	NETMASK |	SUBNETMASK | NID |
| ------ | ---------- | ------- | ---------- | --- |
|A1 |	4	| /29 |	255.255.255.248	| 10.5.0.24 |
|A2	| 2	| /30	| 255.255.255.252	| 10.5.0.0 |
|A3	| 63	| /25	| 255.255.255.128 | 10.5.0.128 |
|A4	| 701	| /22	| 255.255.252.0	| 10.5.4.0 |
|A5	| 2	| /30 |	255.255.255.252	| 10.5.0.4 |
|A6	| 256	| /23	| 255.255.254.0	| 10.5.2.0 |
|A7	| 201	| /24	| 255.255.255.0	| 10.5.1.0 |
|A8	| 4	| /29	| 255.255.255.248	| 10.5.0.16 |
| TOTAL |	1233	| /21	| 255.255.248.0|      |	

## Routing

### Strix
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
hwaddress ether b6:23:6b:b5:5d:33

auto eth1
iface eth1 inet static
address 10.6.7.149
netmask 255.255.255.252

auto eth2
iface eth2 inet static
address 10.6.7.145
netmask 255.255.255.252

```

### Westalis
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.6.7.146
netmask 255.255.255.252
gateway 10.6.7.145

auto eth1
iface eth1 inet static
address 10.6.0.1
netmask 255.255.252.0

auto eth2
iface eth2 inet static
address 10.6.7.1
netmask 255.255.255.128

auto eth3
iface eth3 inet static
address 10.6.7.129
netmask 255.255.255.248
```

### Ostania
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.6.7.150
netmask 255.255.255.252
gateway 10.6.7.149

auto eth1
iface eth1 inet static
address 10.6.4.1
netmask 255.255.254.0

auto eth2
iface eth2 inet static
address 10.6.7.137
netmask 255.255.255.248

auto eth3
iface eth3 inet static
address 10.6.6.1
netmask 255.255.255.0
```
### Client
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
```

### Eden
```
auto eth0
iface eth0 inet static
address 10.6.7.130
netmask 255.255.255.248
gateway 10.6.7.129
```

### Wise
```
auto eth0
iface eth0 inet static
address 10.6.7.131
netmask 255.255.255.248
gateway 10.6.7.129
```

### Garden
```
auto eth0
iface eth0 inet static
address 10.6.7.138
netmask 255.255.255.248
gateway 10.6.7.137
```

### SSS
```
auto eth0
iface eth0 inet static
address 10.6.7.139
netmask 255.255.255.248
gateway 10.6.7.137
```

## Soal 1
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.

Pada Strix ditambahkan konfigurasi:
```
iptables -t nat -A POSTROUTING -s 10.6.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.213
```
## Soal 2
Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.

Pada WISE ditambahan konfigurasi:
```
iptables -A FORWARD -d 10.6.7.128/29 -i eth0 -p tcp --dport 80 -j DROP
iptables -A FORWARD -d 10.6.7.128/29 -i eth0 -p udp --dport 80 -j DROP
```
## Soal 3
Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
Pembahsan Pada DHCP Server (WISE) dilakukan konvigurasi dengan syntax berikut :
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -$
```
konfigurasi diatas akan melakukan reject bila terdapat ping lebih dari 3

## Soal 4
Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.

Pembahasan :

Melakukan pembatasan pada Web Server, yakni Garden dan SSS dengan menggunakan syntax berikut pada
-DNS Server (Eden) :
```
iptables -A INPUT -s 10.6.7.136/29 -m time --timestart 07:00 --timestop 16:0$ 
iptables -A INPUT -s 10.6.7.136/29 -j REJECT
```
-Node Garden dan SSS (Web Server)
```
iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -j REJECT
```
## Soal 5
Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.

Pembahasan Melakukan konfigurasi berikut pada DNS Server (Eden) :
```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.6.7.138 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.6.7.138:80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.6.7.138 -j DNAT --to-destination 10.6.7.139:80
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.6.7.139 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.6.7.139:443
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.6.7.139 -j DNAT --to-destination 10.6.7.138:443
```
Setting

- /var/www/html/index.html
```
# Garden
Garden

# SSS
SSS
```
- /etc/apache2/ports.conf
```
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 443

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
## Soal 6
Karena Loid ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level.

Pembahasan

Melakukan konfigurasi berikut pada DNS Server (Eden) dan DHCP SERVER (Wise):
```
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -$
iptables -A LOGGING - LOG --log-prefix "IPTables-Dropped: "
iptables -A LOGGING -j DROP
```
Melakuakn restart rsyslog pada (Eden) dan DHCP SERVER (Wise), menggunakan syntax :
```
service rsyslog restart
```
