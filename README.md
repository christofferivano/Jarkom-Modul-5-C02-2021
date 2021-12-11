# Jarkom-Modul-5-C02-2021

|         Nama        |       NRP      |
|        :----:       |     :----:     |
| Muhammad Bagus I    | 05111940000049 |
| Christoffer Ivano   | 05111940000091 |
| Bayu Adjie Sidharta | 05111940000172 |

### A. Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Luffy dibawah ini:  

Pertaman buatlah topologi seperti disoal dalam GNS3  
![image](https://user-images.githubusercontent.com/31591861/145675795-a5acff4f-8faa-44c6-9e18-1cbe7a31b397.png)

### B. Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM. setelah melakukan subnetting  

Tentukan jumlah alamat IP yang dibutuhkan oleh tiap subnet dan lakukan labelling netmask berdasarkan jumlah IP yang dibutuhkan.

| Subnet | Jumlah IP | Netmask |
| ------ | --------- | ------- |
| A1     | 3         | /29     |
| A2     | 101       | /25     |
| A3     | 701       | /22     |
| A4     | 2         | /30     |
| A5     | 2         | /30     |
| A6     | 301       | /23     |
| A7     | 201       | /24     |
| A8     | 3         | /29     |
| Total  | 1314      | /21     |

Hitung pembagian IP berdasarkan netmask tersebut menggunakan pohon pada gambar berikut, kemudian lakukan subnetting dengan menggunakan pohon tersebut untuk pembagian IP sesuai dengan kebutuhan masing-masing subnet yang ada :

![vlsm drawio](https://user-images.githubusercontent.com/31591861/145675875-bccbc834-0cbb-4fb7-8f13-d8c458e901d3.png)

Dari pohon dari pohon tersebut akan mendapat pembagian IP sebagai berikut :

| ID | Keterangan        | Address         |
|----|-------------------|-----------------|
| A1 | NID               | 10.15.0.8/29    |
|    | Netmask           | 255.255.255.248 |
|    | Broadcast Address | 10.15.0.15      |
| A2 | NID               | 10.15.0.128/25  |
|    | Netmask           | 255.255.255.128 |
|    | Broadcast Address | 10.15.0.255     |
| A3 | NID               | 10.15.4.0/22    |
|    | Netmask           | 255.255.252.0   |
|    | Broadcast Address | 10.15.7.255     |
| A4 | NID               | 10.15.0.0/30    |
|    | Netmask           | 255.255.255.252 |
|    | Broadcast Address | 10.15.0.3       |
| A5 | NID               | 10.15.0.4/30    |
|    | Netmask           | 255.255.255.252 |
|    | Broadcast Address | 10.15.0.7       |
| A6 | NID               | 10.15.2.0/23    |
|    | Netmask           | 255.255.254.0   |
|    | Broadcast Address | 10.15.3.255     |
| A7 | NID               | 10.15.1.0/24    |
|    | Netmask           | 255.255.255.0   |
|    | Broadcast Address | 10.15.1.255     |
| A8 | NID               | 10.15.0.16/29   |
|    | Netmask           | 255.255.255.248 |
|    | Broadcast Address | 10.15.0.255     |


### C. Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.  

setiap node dikonfigurasi dengan cara `Configure > Edit Network Configuration`. Berikut merupakan salah satu contoh, yaitu menggunakan node `Foosha`:

Pada Edit Network Configuration Foosha, isi konfigurasi masing-masing jalur berdasarkan subnetting yang sudah dibuat sebelumnya:

```
# DHCP config for eth0
 auto eth0
 iface eth0 inet dhcp
# Static config for eth1
auto eth1
iface eth1 inet static
	address 10.15.0.1
 	netmask 255.255.255.252
#	gateway 192.168.1.1
#	up echo nameserver 192.168.1.1 > /etc/resolv.conf

# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.15.0.5
	netmask 255.255.255.252
#	gateway 192.168.2.1
#	up echo nameserver 192.168.2.1 > /etc/resolv.conf
```

### Routing

Routing dari semua router akan menjadi

#### Water7
```
route add -net 0.0.0.0 netmask 0.0.0.0 gateway 10.15.0.1
```

#### Foosha
```
route add -net 10.15.0.8 netmask 255.255.255.248 gateway 10.15.0.2
route add -net 10.15.0.128 netmask 255.255.255.128 gateway 10.15.0.2
route add -net 10.15.4.0 netmask 255.255.252.0 gateway 10.15.0.2
route add -net 10.15.2.0 netmask 255.255.254.0 gateway 10.15.0.6
route add -net 10.15.1.0 netmask 255.255.255.0 gateway 10.15.0.6
route add -net 10.15.0.16 netmask 255.255.255.248 gateway 10.15.0.6
```

#### Guanhao
```
route add -net 0.0.0.0 netmask 0.0.0.0 gateway 10.15.0.5
```

### D. Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.  

Di Foosha, tambahkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.15.0.0/21` untuk mendapatkan akses ke internet dan untuk semua server dan relay lainya, tambahkan `echo namersever 192.168.122.1 > /etc/resolv.conf` untuk mendapatkan akses ke internet.

#### Relay 
Pertama-tama install `isc-dhcp-relay` di 3 titik relay yaitu: Water7, Foosha dan Guanhao
lalu ganti `/etc/default/isc-dhcp-relay` dengan setting yang berbeda beda di setiap router

#### FOOSHA
```
# What servers should the DHCP relay forward requests to?
SERVERS="10.15.0.11"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

#### Guanhao
```
# What servers should the DHCP relay forward requests to?
SERVERS="10.15.0.11"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth0 eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

#### Water7
```
# What servers should the DHCP relay forward requests to?
SERVERS="10.15.0.11"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth3 eth0 eth1 eth2"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

Disini server adalah server dhcp yang akan digunakan, dan interface adalah eth yang terkoneksi kepada client dan router lain.   


#### Server

Pertama-tama install `isc-dhcp-server` di jipangu
lalu ganti `/etc/default/isc-dhcp-server` dengan setting yang akan digunakan

```
# Defaults for isc-dhcp-server initscript
# sourced by /etc/init.d/isc-dhcp-server
# installed at /etc/default/isc-dhcp-server by the maintainer scripts

#
# This is a POSIX shell fragment
#

# Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPD_CONF=/etc/dhcp/dhcpd.conf

# Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPD_PID=/var/run/dhcpd.pid

# Additional options to start dhcpd with.
#       Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACES="eth0"
```

Interface disini adalah eth yang terkoneksi kepada client dan router lain. 

Lalu ubah `/etc/dhcp/dhcpd.conf` Berdasrkan cara subnetting di modul 3 dan hasil subnetting diatas

```
subnet 10.15.0.8  netmask 255.255.255.248 {
}

subnet 10.15.0.128  netmask 255.255.255.128 {
}
subnet 10.15.4.0  netmask 255.255.252.0 {
    range 10.15.4.2 10.15.7.255;
    option routers 10.15.4.1;
    option broadcast-address 10.15.7.255;
    option domain-name-servers 8.8.8.8;
    default-lease-time 600;
    max-lease-time 7200;
}
subnet 10.15.2.0  netmask 255.255.254.0 {
    range 10.15.2.2 10.15.3.255;
    option routers 10.15.2.1;
    option broadcast-address 10.15.3.255;
    option domain-name-servers 8.8.8.8;
    default-lease-time 600;
    max-lease-time 7200;
}
subnet 10.15.1.0  netmask 255.255.255.0 {
    range 10.15.1.2 10.15.1.255;
    option routers 10.15.1.1;
    option broadcast-address 10.15.1.255;
    option domain-name-servers 8.8.8.8;
    default-lease-time 600;
    max-lease-time 7200;
}
subnet 10.15.0.16  netmask 255.255.255.248 {
    range 10.15.0.18 10.15.0.23;
    option routers 10.15.0.17;
    option broadcast-address 10.15.0.23;
    option domain-name-servers 8.8.8.8;
    default-lease-time 600;
    max-lease-time 7200;
}
```

lakukan `service isc-dhcp-server restart` untuk menyalakan DHCP server  

![image](https://user-images.githubusercontent.com/31591861/145676452-fc9bd74d-8c3a-4310-8294-d9905e6cdeb1.png)
![image](https://user-images.githubusercontent.com/31591861/145676459-41c70868-57b4-4b6d-8624-e45fe70316b2.png)


### 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
Input snytax berikut pada Foosha 
```
iptables -t nat -A POSTROUTING -s 10.15.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.25
```
Keterangan : <br>
`-t nat` : Menggunakan tabel NAT karena akan mengubah alamat asal dari paket
`-A POSTROUTING`: Menggunakan chain POSTROUTING karena mengubah asal paket setelah routing
`-s 10.15.0.0/21`: Mendifinisikan alamat asal dari paket yaitu semua alamat IP dari subnet 10.15.0.0/21
`-o eth0` : Paket keluar dari eth0 Foosha
`-j SNAT`: Menggunakan target SNAT untuk mengubah source atau alamat asal dari paket
`--to-s` (ip eth0) : Mendefinisikan IP source, di mana digunakan eth0 Foosha dengan rentang IP 192.168.122.0 sampai 192.168.122.255

Testing : ping ke beberapa node 
Nb : 
1. Untuk subnet blueno dan cipher harus memperhatikan ketentuan pada soal no.4 sehingga tanggal dan waktu harus diatur dengan : 
```
date -s "8 nov 2021 10:00:00"
```
2. Untuk subnet elena dan fukurou harus memperhatikan ketentuan pada soal no.5 sehingga tanggal dan waktu harus diatur dengan : 
```
date -s "8 nov 2021 17:00:00"
```


#### Hasil Testing
1. Doriki

[![doriki-ping.png](https://i.postimg.cc/HWQb3P5z/doriki-ping.png)](https://postimg.cc/F1HfHpTk)

2. Jipangu

[![jipangu-ping.png](https://i.postimg.cc/LXFn12yp/jipangu-ping.png)](https://postimg.cc/XZxjMSSH)

3. Blueno

[![blue-ping.png](https://i.postimg.cc/VLhstz6V/blue-ping.png)](https://postimg.cc/9rGj55yd)

4. Cipher

[![cipher-ping.png](https://i.postimg.cc/Njrs7Z2f/cipher-ping.png)](https://postimg.cc/VJmPzGF3)

5. Jorge

[![jorge.png](https://i.postimg.cc/Dfj7V7qw/jorge.png)](https://postimg.cc/Z0dGpt7G)

6. Maingate

[![main-ping.png](https://i.postimg.cc/bNZpp4TH/main-ping.png)](https://postimg.cc/RNxjTs2N)

7. Elena

[![ele.png](https://i.postimg.cc/GtV04hQP/ele.png)](https://postimg.cc/jnH39TBC)

8. Fukurou

[![fukurou.png](https://i.postimg.cc/nh8bZrMp/fukurou.png)](https://postimg.cc/MvYrmWyF)


### 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
Input syntax berikut pada Doriki dan Jipangu :<br>
```
iptables -A FORWARD -d 10.15.0.8/29 -i eth0 -p tcp --dport 80 -j DROP
```
Keterangan :<br>
`-A FORWARD`: Menggunakan chain FORWARD<br>
`-p tcp` : Mendefinisikan protokol yang digunakan, yaitu tcp<br>
`--dport 80` : Mendefinisikan port yang digunakan, yaitu 80 (HTTP)<br>
`-d 10.15.0.8/29` : Mendefinisikan alamat tujuan dari paket (DHCP dan DNS SERVER ) berada pada subnet 10.15.0.8/29<br>
`-i eth0` : Paket masuk dari eth0 Foosha<br>
`-j DROP` : Paket di-drop<br>

Testing :<br>
1. Install netcat di server Jipangu dan Doriki: apt-get install netcat<br>
2. Pada Jipangu dan Doriki ketikkan: nc -l -p 80<br>
3. Pada foosha ketikkan: nmap -p 80 10.15.0.10 atau nmap -p 80 10.15.0.11<br>

Hasil :<br>
![image](https://user-images.githubusercontent.com/73484021/145559005-cb408fd1-2e19-47f0-a5f7-b2252ec57b82.png)

### 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
Input syntax berikut pada Doriki dan Jipangu :<br>
```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
Keterangan :<br>
`-A INPUT` : Menggunakan chain INPUT<br>
`-p icmp` : Mendefinisikan protokol yang digunakan, yaitu ICMP (ping)<br>
`-m connlimit` : Menggunakan rule connection limit<br>
`--connlimit-above 3` : Limit yang ditangkap paket adalah di atas 3<br>
`--connlimit-mask 0` : Hanya memperbolehkan 3 koneksi setiap subnet dalam satu waktu<br>
`-j DROP` : Paket di-drop<br>

Testing :<br>
1. Masuk ke 4 node berbeda<br>
2. ping ke arah Jipangu/Doriki secara bersamaan<br>

Hasil :<br>
1. Node 1 (Foosha)<br>
![image](https://user-images.githubusercontent.com/73484021/145559876-19390086-4ca0-4cbf-b2bc-5f65ad7d1397.png)
2. Node 2 (Water7)<br>
![image](https://user-images.githubusercontent.com/73484021/145560031-c35d995f-988b-4728-bcd2-af9be9f38698.png)
3. Node 3 (Guanhao)<br>
![image](https://user-images.githubusercontent.com/73484021/145559980-74ca689f-61d4-4232-b8ad-8d2775f3a3ba.png)
4. Node 4 (Blueno)<br>
![image](https://user-images.githubusercontent.com/73484021/145560114-f28c1535-a03b-4964-acf0-87384ff77d03.png)

### 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.
Input syntax berikut pada Doriki :<br>
```
iptables -A INPUT -s 10.15.0.128/25 -d 10.15.0.8/29 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.15.0.128/25 -j REJECT
iptables -A INPUT -s 10.15.4.0/22 -d 10.15.0.8/29 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.15.4.0/22 -j REJECT
```
Keterangan :<br>
`A INPUT` : Menggunakan chain INPUT<br>
`m time` : Menggunakan rule time<br>
`-timestart 07:00` : Mendefinisikan waktu mulai yaitu 07:00<br>
`-timestop 15:00` : Mendefinisikan waktu berhenti yaitu 15:00<br>
`--weekdays Mon,Tue,Wed,Thu` : Mendefinisikan hari yaitu Senin hingga Kamis<br>
`-j ACCEPT` : Paket di-accept<br>
`-j REJECT` : Paket ditolak<br>

Testing :<br>
Set waktu pada Doriki dengan syntax `date -s "8 nov 2021 10:00:00"` lalu ping Doriki dari `Blueno` dan `Cipher`.<br>

Hasil :<br>
![image](https://user-images.githubusercontent.com/73484021/145560854-afcbe781-6601-42eb-8609-8c21271ed21c.png)
![image](https://user-images.githubusercontent.com/73484021/145560887-783496cd-1d9f-4352-8d05-30b68bf07a80.png)

### 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.
Input syntax berikut pada Doriki :<br>
```
iptables -A INPUT -s 10.15.2.0/23 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 10.15.2.0/23 -j REJECT
iptables -A INPUT -s 10.15.1.0/24 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT 
iptables -A INPUT -s 10.15.1.0/24 -j REJECT
```
Keterangan :<br>
`A INPUT` : Menggunakan chain INPUT<br>
`m time` : Menggunakan rule time<br>
`-timestart 15:01` : Mendefinisikan waktu mulai yaitu 07:00<br>
`-timestop 06:59` : Mendefinisikan waktu berhenti yaitu 15:00<br>
`-j ACCEPT` : Paket di-accept<br>
`-j REJECT` : Paket ditolak<br>

Testing :<br>
Set waktu pada Doriki dengan syntax `date -s "8 nov 2021 17:00:00"` lalu ping Doriki dari `Elena` dan `Fukurou`.<br>

Hasil :<br>
![image](https://user-images.githubusercontent.com/73484021/145561471-2601e816-f678-43b0-8c9f-6884a03d5ffe.png)
![image](https://user-images.githubusercontent.com/73484021/145561501-96a31bde-bb40-4a24-b3dc-f09012bfb5c4.png)

### 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate
Input syntax berikut pada Guanhao, Jorge, dan Maingate :<br>
```
iptables -A PREROUTING -t nat -p tcp -d 10.15.0.8/29 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination  10.15.0.18:80
iptables -A PREROUTING -t nat -p tcp -d 10.15.0.8/29 --dport 80 -j DNAT --to-destination 10.15.0.19:80
iptables -t nat -A POSTROUTING -p tcp -d 10.15.0.18 --dport 80 -j SNAT --to-source 10.15.0.8:80
iptables -t nat -A POSTROUTING -p tcp -d 10.15.0.19 --dport 80 -j SNAT --to-source 10.15.0.8:80
```

Testing : <br>
1. Pada Guanhao, Jorge, Maingate dan Elena install apt-get install netcat
2. Pada Jorge ketikkan perintah: nc -l -p 80
3. Pada Maingate ketikkan perintah: nc -l -p 80
4. Pada client Elena ketikkan perintah: nc 10.15.0.8 80
5. Ketikkan sembarang pada client Elena, nanti akan muncul bergantian


#### Hasil Testing 

[![jorge.png](https://i.postimg.cc/Dfj7V7qw/jorge.png)](https://postimg.cc/Z0dGpt7G)

[![ele.png](https://i.postimg.cc/GtV04hQP/ele.png)](https://postimg.cc/jnH39TBC)

[![main.png](https://i.postimg.cc/Gp567mWW/main.png)](https://postimg.cc/XZ92X47L)

[![fuku.png](https://i.postimg.cc/ZKS2DgQ5/fuku.png)](https://postimg.cc/LhxxJyRc)
