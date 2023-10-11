<h1>Praktikum Modul 2 Jaringan Komputer</h1>
<h3>Kelompok I07</h3>

| NAME                          | NRP       |
|-------------------------------|-----------|
|Pascal Roger Junior Tauran     |5025211072 |
|Mochammad Naufal Ihza Syahzada |5025211260 |



## Number 1
```
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut
```
Tapology:<br>
![Number1Tapology](https://cdn.discordapp.com/attachments/824131614073683968/1161343999047635066/image.png?ex=6537f4e2&is=65257fe2&hm=174ff5cd70598ac89e6b8dda764ac49db96e4234ade089ba574dd495d0e2a570&)

### Router
```sh
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 10.62.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 10.62.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 10.62.3.1
    netmask 255.255.255.0
```

### NakulaClient

```sh
auto eth0
iface eth0 inet static
    address 10.62.1.2
    netmask 255.255.255.0
    gateway 10.62.1.1
```

### SadewaClient

```sh
auto eth0
iface eth0 inet static
    address 10.62.1.3
    netmask 255.255.255.0
    gateway 10.62.1.1
```

### ArjunaLoadBalancer

```sh
auto eth0
iface eth0 inet static
    address 10.62.1.4
    netmask 255.255.255.0
    gateway 10.62.1.1
```

### WisanggeniWebServer

```sh
auto eth0
iface eth0 inet static
    address 10.62.3.2
    netmask 255.255.255.0
    gateway 10.62.3.1
```

### AbimanyuWebServer

```sh
auto eth0
iface eth0 inet static
    address 10.62.3.3
    netmask 255.255.255.0
    gateway 10.62.3.1
```

### PrabukusumaWebServer

```sh
auto eth0
iface eth0 inet static
    address 10.62.3.4
    netmask 255.255.255.0
    gateway 10.62.3.1
```

### WerkudaDNSSlave

```sh
auto eth0
iface eth0 inet static
    address 10.62.2.3
    netmask 255.255.255.0
    gateway 10.62.2.1
```

### YudhistiraDNSMaster

```sh
auto eth0
iface eth0 inet static
    address 10.62.2.2
    netmask 255.255.255.0
    gateway 10.62.2.1
```

Explanation:
- Add the following command to the /root/.bashrc file using the nano text editor on the "Route" node:
```sh
nano /root/.bashrc
```
Inside the nano editor, add the following line at the end of the file:
```sh
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.62.0.0/16
```
Save the file and exit the nano editor by pressing Ctrl + X, followed by Y to confirm the changes, and then Enter.
- To find the name server, we run the following command on the "Route" node:
```sh
cat /etc/resolv.conf
```
- Then we add the following command to set the nameserver to `192.168.122.1` by adding it to the `.bashrc` file of every other `ubuntu` that isn't the router using the `nano ~/.bashrc` command with:
```sh
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

## Number 2
```
Buatlah website utama dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
```
Solution:<br>
![arjuna.I07.com](https://cdn.discordapp.com/attachments/824131614073683968/1161349543913345084/image.png?ex=6537fa0c&is=6525850c&hm=1f1ffd0016534c29338295a66e58651d69840a760252a86b73cd894a696e06bb&)

![/etc/bind/named.conf.local](https://cdn.discordapp.com/attachments/824131614073683968/1161349900517265458/image.png?ex=6537fa61&is=65258561&hm=aa0f711c0a5e79c400c693bd437e947fc2b65a64f88890a9580ac885eb7e804d&)

![number2terminal](https://cdn.discordapp.com/attachments/824131614073683968/1161350203064983582/image.png?ex=6537faa9&is=652585a9&hm=031f91c2b724b2f4c0f3b57b2347524b19345d0d06e7df7e11a8055da0257701&)

Explanation:
- We first run `apt update` and `apt install bind9 -y` to install bind9 in `YushistiraDNSMaster`
- We then edit `the named.conf.local` file in `root` directory with the  command
```sh
nano /root/named.conf.local
```
- We then add
```sh
zone "arjuna.I07.com" {
	type master;
	file "/etc/bind/jarkom/arjuna.I07.com";
};
```
to the end of the `the named.conf.local` file
- Afterwards we make a folder `jarkom` in the `root`directory with the command `mkdir jarkom` and the copy the  the `db.local` file in the `/etc/bind` directory to the `jarkom` folder in the in the `root` directory and rename it `arjuna.I07.com` with the command
```sh
cp /etc/bind/db.local /root/jarkom/arjuna.I07.com
```
- We then edit the `arjuna.I07.com` file changing some of the contents to `arjuna.I07.com.` along with adding the IP of `YushistiraDNSMaster`; `10.62.2.2` and adding another line
```sh
www     IN      CNAME       arjuna.I07.com.
```
to make an alias `www.arjuna.I07.com`

- We then edit the `.bashrc` file with the command `nano ~/.bashrc` in `WerkudaDNSSlave`
```sh
echo nameserver 10.62.2.2 > /etc/resolv.conf
```
to alter the `/etc/resolv.conf` every time the system is restarted.
- We test its functionality this by running 
```sh
host -t CNAME www.arjuna.I07.com
```
to see if `www.arjuna.I07.com` is and alias for `arjuna.I07.com` and 
```sh
ping www.arjuna.I07.com -c 5
```

## Number 3
```
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
```
Solution:<br>
![/etc/bind/named.conf.local](https://cdn.discordapp.com/attachments/824131614073683968/1161357806688026625/image.png?ex=653801be&is=65258cbe&hm=a7d88f73c98c85c660ef7bb5ea42bb162a9106cbb4810114bed70576d0b208f9&)

![abimanyu.I07.com](https://cdn.discordapp.com/attachments/824131614073683968/1161357876636430497/image.png?ex=653801ce&is=65258cce&hm=51ad7d2195f472f4706e25cb5be0dcdf63c47ed799803b252ce3259e21aae5a2&)

![number3terminal](https://cdn.discordapp.com/attachments/824131614073683968/1161357920764690603/image.png?ex=653801d9&is=65258cd9&hm=9b8c4412f45e25e5d1865a88021e5a9e3d7dfe6178caf26d442d3ff03ca70178&)

Explanation:
- We edit `the named.conf.local` file in `root` directory with the  command
```sh
nano /root/named.conf.local
```
- We then add
```sh
zone "abimanyu.I07.com" {
	type master;
	file "/etc/bind/jarkom/abimanyu.I07.com";
};
```
to the end of the `the named.conf.local` file
- Afterwards we copy the  the `db.local` file in the `/etc/bind` directory to the `jarkom` folder in the in the `root` directory and rename it `abimanyu.I07.com` with the command
```sh
cp /etc/bind/db.local /root/jarkom/abimanyu.I07.com
```
- We then edit the `abimanyu.I07.com` file changing some of the contents to `abimanyu.I07.com.` along with adding the IP of `YushistiraDNSMaster`; `10.62.2.2` and adding another line
```sh
www     IN      CNAME       abimanyu.I07.com.
```
to make an alias `www.abimanyu.I07.com`
- We test its functionality this by running 
```sh
host -t CNAME www.abimanyu.I07.com
```
to see if `www.abimanyu.I07.com` is and alias for `abimanyu.I07.com` and 
```sh
ping www.abimanyu.I07.com -c 5
```
## Number 4
```
Berapa nilai checksum yang didapat dari header pada paket nomor 130?
```
Solution:<br>

![Number4_image](https://media.discordapp.net/attachments/824131614073683968/1154435177255288943/Screenshot_2023-09-18_205640.png?width=681&height=671)  

```sh
Frame 130: 196 bytes on wire (1568 bits), 196 bytes captured (1568 bits) on interface \Device\NPF_{3F96C891-C513-447E-9B66-6C81A5C93077}, id 0
Ethernet II, Src: 02:e4:90:54:f3:42 (02:e4:90:54:f3:42), Dst: zte_e8:05:1c (28:c8:7c:e8:05:1c)
Internet Protocol Version 4, Src: 192.168.1.19, Dst: 74.125.130.139
User Datagram Protocol, Src Port: 57296, Dst Port: 443
    Source Port: 57296
    Destination Port: 443
    Length: 162
    Checksum: 0x18e5 [unverified]
    [Checksum Status: Unverified]
    [Stream index: 9]
    [Timestamps]
    UDP payload (154 bytes)
QUIC IETF
QUIC IETF
```
Explanation:
- First go to the Header number 130, afterwards double click it.
- Then click on the `User Datagram Protocol`, it will open a drop down tab.
- There we can search for the `Checksum value`, which in this case is `0x18e5` 

## Number 5
```
Elshe menemukan suatu file packet capture yang menarik. Bantulah Elshe untuk menganalisis file packet capture tersebut.
```
### 5A

```
Berapa banyak packet yang berhasil di capture dari file pcap tersebut?
```
Solution:<br>
```sh
//No current solution
```
Explanation:
- `No current explanation` 

### 5B
```
Port berapakah pada server yang digunakan untuk service SMTP?
```
Solution:<br>
```sh
//No current solution
```
Explanation:
- `No current explanation` 

### 5C

```
Dari semua alamat IP yang tercapture, IP berapakah yang merupakan public IP?
```
Solution:<br>
```sh
//No current solution
```
Explanation:
- `No current explanation` 

## Number 6
```
Seorang anak bernama Udin Berteman dengan SlameT yang merupakan seorang penggemar film detektif. sebagai teman yang baik, Ia selalu mengajak slamet untuk bermain valoranT bersama. suatu malam, terjadi sebuah hal yang tak terdUga. ketika udin mereka membuka game tersebut, laptop udin menunjukkan sebuah field text dan Sebuah kode Invalid bertuliskan "server SOURCE ADDRESS 7812 is invalid". ketika ditelusuri di google, hasil pencarian hanya menampilkan a1 e5 u21. jiwa detektif slamet pun bergejolak. bantulah udin dan slamet untuk menemukan solusi kode error tersebut.
```
Solution:<br>

```sh
//No current solution
```

Explanation:
- `No current explanation` 

## Number 7
```
Berapa jumlah packet yang menuju IP 184.87.193.88?
```
Solution:<br>

![Number7_image](https://media.discordapp.net/attachments/906158395617320980/1154435868950548531/Screenshot_2023-09-18_193024.png)  

```sh
ip.dst == 184.87.193.88
```

Explanation:
- Since the question asks for the packets that are  `menuju IP 184.87.193.88`, it means that the `destination` of the packets are `184.87.193.88`
- `ip.dst` is for filtering the `IP destinations` which in this case is  `184.87.193.88`
- Afterwards we count number of packets which are `6`

## Number 8
```
Berikan kueri filter sehingga wireshark hanya mengambil semua protokol paket yang menuju port 80! (Jika terdapat lebih dari 1 port, maka urutkan sesuai dengan abjad)
```
Solution:<br>
![1 8 1](https://github.com/Pascalrjt/Jarkom-Modul-1-I07-2023/assets/89951546/fd588b77-3340-4360-bc6b-9a46d1c5bed9)

Explanation:
- There will be two filter, first `tcp.dstport == 80` and `udp.dstport == 80`
- Please note that after the dot, you need to include `dst` which stands for destination
- Combine those two filter with `||`, so it will be  `tcp.dstport == 80 || udp.dstport == 80`

## Number 9
```
Berikan kueri filter sehingga wireshark hanya mengambil paket yang berasal dari alamat 10.51.40.1 tetapi tidak menuju ke alamat 10.39.55.34!
```
Solution:<br>

![Number9_image](https://media.discordapp.net/attachments/824131614073683968/1154435176940711996/Screenshot_2023-09-18_203242.png?width=627&height=671)

```sh
ip.src == 10.51.40.1 && ip.dst != 10.39.55.34
```

Explanation:
- The question asks for packets that `berasal dari alamat 10.51.40.1 tetapi tidak menuju ke alamat 10.39.55.34` which means that it is looking for packets which `source` is IP `10.51.40.1` and the `destination` is not IP `10.39.55.34` 
- `ip.src` is for filtering the `IP sources` which in this case is  `10.51.40.1`
- `ip.dst` is for filtering the `IP desinations` which in this case is NOT `10.39.55.34` which is denoted by the `!=`.

## Number 10
```
Sebutkan kredensial yang benar ketika user mencoba login menggunakan Telnet
```

Solution:<br>

![Number10_image](https://media.discordapp.net/attachments/824131614073683968/1154435176592592996/Screenshot_2023-09-18_202027.png)

```sh
Frame 87: 92 bytes on wire (736 bits), 92 bytes captured (736 bits) on interface tap2, id 0
Ethernet II, Src: 0c:c8:32:27:00:00 (0c:c8:32:27:00:00), Dst: 26:82:4f:e4:c2:07 (26:82:4f:e4:c2:07)
Internet Protocol Version 4, Src: 172.16.0.4, Dst: 172.16.0.254
Transmission Control Protocol, Src Port: 23, Dst Port: 57784, Seq: 13, Ack: 26, Len: 26
Telnet
    Data: dhafin:kesayangannyak0k0\r\n
```

Explanation:
- First we filter out with `telnet`
- Then we browse the headers and look into the `Telnet` drop down tab until we find one where the data resembles a username and password which we find in `header number 87` and the login credentials are `dhafin:kesayangannyak0k0`
