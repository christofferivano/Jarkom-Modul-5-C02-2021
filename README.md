# Jarkom-Modul-5-C02-2021

### 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
```
iptables -A FORWARD -d 10.15.0.8/29 -i eth0 -p tcp --dport 80 -j DROP
```
Keterangan :
`-A FORWARD`: Menggunakan chain FORWARD
`-p tcp` : Mendefinisikan protokol yang digunakan, yaitu tcp
`--dport 80` : Mendefinisikan port yang digunakan, yaitu 80 (HTTP)
`-d 10.15.0.8/29` : Mendefinisikan alamat tujuan dari paket (DHCP dan DNS SERVER ) berada pada subnet 10.15.0.8/29
`-i eth0` : Paket masuk dari eth0 Foosha
`-j DROP` : Paket di-drop
