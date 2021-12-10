# Jarkom-Modul-5-C02-2021

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
