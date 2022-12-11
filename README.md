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
