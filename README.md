# Jarkom-Modul-5-D06-2021

Anggota :
- Fitrah Mutiara 05111940000126
- Ahdan Amanullah Irfan Muzhaffar 05111940000197
- Dewi Mardani 05111940000225

## Setting Topologi
- Pertama buat topologi seperti berikut:

![image](https://user-images.githubusercontent.com/58657135/145675204-1d0603f5-ce81-46e2-8877-027223838691.png)

- Lalu telah dilakukan perhitungan CIDR berikut untuk mengatur konfigurasi dari tiap subnet:

![image](https://user-images.githubusercontent.com/58657135/145675362-27ff4141-8b1c-4778-9ea6-eba88824c87e.png)

![image](https://user-images.githubusercontent.com/58657135/145675372-61538cd2-5249-4aef-9328-e50e9e6054c0.png)

![image](https://user-images.githubusercontent.com/58657135/145675377-b828e1da-3356-4a1c-a3f8-e9c8fdb8509d.png)

![image](https://user-images.githubusercontent.com/58657135/145675383-7196aa8a-896a-4fb8-bb15-09cc87594158.png)

![image](https://user-images.githubusercontent.com/58657135/145675387-6f653040-a789-47e6-b15a-ab35eb4fa63b.png)

Pohon Faktor:

![image](https://user-images.githubusercontent.com/58657135/145675422-e045d1d2-a1d3-4525-b6ef-38fcd8379ac2.png)

Tabel NID, Subnet, Broadcast Address:

![image](https://user-images.githubusercontent.com/58657135/145675450-6fb7a1a7-1b33-4dd9-b9bd-7e12c36a8fcb.png)

- Berikut konfigurasi IP tiap Node:

Foosha:
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.24.16.1
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 10.24.40.1
	netmask 255.255.255.252
```

Water7:
```
auto eth0
iface eth0 inet static
        address 10.24.16.2
        netmask 255.255.255.252
        gateway 10.24.16.1

auto eth1
iface eth1 inet static
	address 10.24.4.1
	netmask 255.255.255.128

auto eth2
iface eth2 inet static
	address 10.24.0.1
	netmask 255.255.252.0

auto eth3
iface eth3 inet static
	address 10.24.8.1
	netmask 255.255.255.248
```

Guanhao:
```
auto eth0
iface eth0 inet static
	address 10.24.40.2
	netmask 255.255.255.252
	gateway 10.24.40.1

auto eth1
iface eth1 inet static
	address 10.24.32.1
	netmask 255.255.254.0

auto eth2
iface eth2 inet static
	address 10.24.34.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.24.36.1
	netmask 255.255.255.248
```

Doriki:
```
auto eth0
iface eth0 inet static
	address 10.24.8.2
	netmask 255.255.255.248
	gateway 10.24.8.1
```

Jipangu:
```
auto eth0
iface eth0 inet static
        address 10.24.8.3
        netmask 255.255.255.248
        gateway 10.24.8.1
```

Jorge:
```
auto eth0
iface eth0 inet static
        address 10.24.36.2
        netmask 255.255.255.248
        gateway 10.24.36.1
```

Maingate:
```
auto eth0
iface eth0 inet static
	address 10.24.36.3
	netmask 255.255.255.248
	gateway 10.24.36.1
```

Blueno, Cipher, Elena, Fukurou:
```
auto eth0
iface eth0 inet dhcp
```

## Lakukan Routing pada FOOSHA
```
route add -net 10.24.32.0 netmask 255.255.240.0 gw 10.24.40.2
route add -net 10.24.0.0 netmask 255.255.224.0 gw 10.24.16.2
```
## Test DHCP

![image](https://user-images.githubusercontent.com/58657135/145675639-9539787d-7eb7-46aa-81a3-c66b875d27b3.png)

## 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
### Pada Foosha
`iptables -t nat -A POSTROUTING -s 10.24.0.0/18 -o eth0 -j SNAT --to-source 10.24.8.3`
### Pada Water7
Test dengan `ping google.com`

<img width="569" alt="image" src="https://user-images.githubusercontent.com/81247727/145370455-a8757bbe-0bb4-4841-ba44-c1737d577d1a.png">

## 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang memiliki ip DHCP dan DNS Server demi menjaga keamanan.
### Pada Water7
```
iptables -A FORWARD -d 10.24.8.3 -p tcp --dport 80 -j REJECT
iptables -A FORWARD -d 10.24.8.2 -p tcp --dport 80 -j REJECT
```
### Test Jipangu
Test dengan `nmap -p 80 10.24.8.3`

<img width="420" alt="image" src="https://user-images.githubusercontent.com/58657135/145675773-8c6db267-a699-4cdc-9c24-0fd331118252.png">

### Test Doriki
Test dengan `nmap -p 80 10.24.8.2`

<img width="420" alt="image" src="https://user-images.githubusercontent.com/58657135/145675732-59cd3f3d-4dac-4ced-beda-b0936793bbc7.png">

Jika state http pada IP tersebut sudah berstatus `filtered` maka sudah berhasil.

## 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
### Pada Jipangu dan Doriki
`iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP`

Di test pada 3 node untuk ping Doriki/Jipangu. Dikatakan berhasil jika saat lebih dari 3, maka ping akan stuck:

1.

![image](https://user-images.githubusercontent.com/58657135/145675906-a5ef7b58-246d-4fe7-bae3-e21ea085a17a.png)

2.

![image](https://user-images.githubusercontent.com/58657135/145675948-334b2e03-5cc9-4053-be1b-d2c3e08ed058.png)

3.

![image](https://user-images.githubusercontent.com/58657135/145675960-776a7c26-3df7-4184-8d2b-e4c090c37e1d.png)

Lalu ke-4 ping akan stuck (tidak lanjut) begitupun pada 3 koneksi sebelumnya:

4. (Stuck, hanya berjalan 1 kali)

![image](https://user-images.githubusercontent.com/58657135/145675978-27ba1c07-ddc5-4eff-a1e1-b5df19a7783e.png)

Begitu pula dengan koneksi-koneksi sebelumnya akan berhenti/stuck.

## 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis
### Pada Doriki
```
iptables -A INPUT -s 10.24.0.0/22 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.24.0.0/22 -j REJECT
iptables -A INPUT -s 10.24.4.0/25 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.24.4.0/25 -j REJECT
```
### Pada Blueno
Test dengan ping ke Doriki `ping 10.24.8.2`
`date -s "6 DEC 2021 08:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676037-5e9f7fb3-d3ff-41bc-95cd-552bdffbe3f0.png)

`date -s "6 DEC 2021 18:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676046-6713222d-3e22-4f6b-8b5f-27d454744d9e.png)

### Pada Cipher
Test dengan ping ke Doriki `ping 10.24.8.2`
`date -s "6 DEC 2021 08:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676084-83d18187-9565-4420-bec7-15d3184d72cf.png)

`date -s "6 DEC 2021 18:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676063-07d31027-935e-4469-ab5d-98a31908c6ff.png)

## 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.
### Pada Doriki
```
iptables -A INPUT -s 10.24.32.0/23 -m time --timestart 15:01 --timestop 23:59 -j ACCEPT
iptables -A INPUT -s 10.24.34.0/24 -m time --timestart 15:01 --timestop 23:59 -j ACCEPT
iptables -A INPUT -s 10.24.32.0/23 -m time --timestart 00:00 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 10.24.34.0/24 -m time --timestart 00:00 --timestop 06:59 -j ACCEPT

iptables -A INPUT -s 10.24.32.0/23 -j REJECT
iptables -A INPUT -s 10.24.34.0/24 -j REJECT
```
### Pada Elena
Test dengan ping ke Doriki `ping 10.24.8.2`
`date -s "6 DEC 2021 08:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676149-7f4bcd2a-1f91-4a19-b4ed-e3e91453bad8.png)

`date -s "6 DEC 2021 16:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676166-e1eeea3b-172f-42b6-b867-058d87617ead.png)

### Pada Fukurou
Test dengan ping ke Doriki `ping 10.24.8.2`
`date -s "6 DEC 2021 08:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676213-c8e5e40c-89bd-49b0-a6fc-fdfe018237c8.png)

`date -s "6 DEC 2021 16:00:00"`:

![image](https://user-images.githubusercontent.com/58657135/145676198-18c01142-4dfa-4f11-8798-3d5841fc5fec.png)


## 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate
