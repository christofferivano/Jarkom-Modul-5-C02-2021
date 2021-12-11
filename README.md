# Jarkom-Modul-5-C02-2021

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
[doriki-ping.png](https://postimg.cc/F1HfHpTk)

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
