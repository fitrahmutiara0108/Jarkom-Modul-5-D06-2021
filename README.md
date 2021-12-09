# Jarkom-Modul-5-D06-2021

Anggota :
- Fitrah Mutiara 05111940000126
- Ahdan Amanullah Irfan Muzhaffar 05111940000197
- Dewi Mardani 05111940000225

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
### Pada Jipangu
Test dengan `nmap -p 80 10.24.8.3`
<img width="420" alt="image" src="https://user-images.githubusercontent.com/81247727/145370766-beaf08a8-3bd4-4343-be52-7e12b6ca64ee.png">
### Pada Doriki
Test dengan `nmap -p 80 10.24.8.2`
<img width="428" alt="image" src="https://user-images.githubusercontent.com/81247727/145370895-12bf719a-1ced-47cb-a0f4-8e0f9e04a235.png">

## 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
### Pada Jipangu dan Doriki
`iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP`
### Pada Doriki
Test dengan ping ke Jipangu `ping 10.24.8.3`
<img width="392" alt="image" src="https://user-images.githubusercontent.com/81247727/145372380-c84b3ebd-bcfe-49c2-8e3c-f131c333c7a1.png">
### Pada Water7
Test dengan ping ke Jipangu `ping 10.24.8.3`
<img width="377" alt="image" src="https://user-images.githubusercontent.com/81247727/145372496-c15081f2-6939-4182-a6b3-f146336b44b9.png">
### Pada Cipher
Test dengan ping ke Jipangu `ping 10.24.8.3`
### Pada Blueno
Test dengan ping ke Jipangu `ping 10.24.8.3`

## 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis
### Pada Doriki
```
iptables -A INPUT -s 10.24.0.0/22 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.24.0.0/22 -j REJECT
iptables -A INPUT -s 10.24.4.0/25 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.24.4.0/25 -j REJECT
```
### Pada Cipher
Test dengan ping ke Doriki `ping 10.24.8.2`
`date -s "6 DEC 2021 08:00:00"`
`date -s "6 DEC 2021 18:00:00"`
### Pada Blueno
Test dengan ping ke Doriki `ping 10.24.8.2`
`date -s "6 DEC 2021 08:00:00"`
`date -s "6 DEC 2021 18:00:00"`

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
### Pada Blueno
Test dengan ping ke Doriki `ping 10.24.8.2`

## 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate
