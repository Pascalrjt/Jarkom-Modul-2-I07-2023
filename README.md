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
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
```
Solution:<br>

![abimanyu.I08.com](https://cdn.discordapp.com/attachments/824131614073683968/1161658945862127666/image.png?ex=65391a33&is=6526a533&hm=b202ac54b4cc2c26690518b291fcb1059da59e4d4f6ea2b40a0b67695ed2dcd0&) 

![number4result](https://cdn.discordapp.com/attachments/824131614073683968/1161659039420252190/image.png?ex=65391a49&is=6526a549&hm=b4e4dc0860bfb8ccaf24bc16010a8afbc9e054b7d9c7c89fdd82d0a37432c86a&)

Explanation:
- We edit the `abimanyu.I07.com` file in `YudhistiraDNSMaster`, adding a new line
```sh
parikesit   IN  A   10.62.2.2   ; IP YudhistiraDNSMaster
```
- We can then test it by running `host -t A parikesit.abimanyu.I07.com` and `ping parikesit.abimanyu.I07.com -c 5` in `WerkudaraDNSSlave`

## Number 5
```
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
```
Solution:<br>
![named.conf.local](https://media.discordapp.net/attachments/824131614073683968/1161660627153059911/image.png?ex=65391bc4&is=6526a6c4&hm=acaa5e07ab49cd340289bac1baef8c7d4ed0cdaa65fac25f3992722d97df9816&=)

![2.62.10.in-addr.arpa](https://media.discordapp.net/attachments/824131614073683968/1161660721327775886/image.png?ex=65391bda&is=6526a6da&hm=b0840a51eeb20bd7ddec3334e9a83c176b78738a2421bd5053dc9e6c798e77b5&=)

![werkudara.bashrc](https://media.discordapp.net/attachments/824131614073683968/1161660761026854932/image.png?ex=65391be4&is=6526a6e4&hm=c9a0e3f7571c23ff83fa9e5c655c5f149671ed6f90fc88e65c92d808fc569262&=)

![number5result](https://media.discordapp.net/attachments/824131614073683968/1161660811241082930/image.png?ex=65391bf0&is=6526a6f0&hm=8b30511da98bb7b90060eda4d41cb9857671fc835f9177719423f09c294b7110&=)

Explanation:
- We edit the `named.conf.local` file in the `/etc/bind/` directory adding 
```sh
zone "2.62.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.62.10.in-addr.arpa";
};
```
with `2.62.10` being the reverse of our IP `10.62.2`.
- Afterwards we create the file `2.62.10.in-addr.arpa` in the `/etc/bind/jarkom/` direcory which is based on the `db.local` file and altering it by replacing some values with `abimanyu.I07.com` and adding
```sh
2.62.10.in-addr.arpa    IN  NS      abimanyu.I07.com.
2                       IN  PTR     abimanyu.I07.com.
```
-  In `WerkudaraDNSMaster` we edit the `.bshrc` script to become
```sh
echo nameserver 192.168.122.1 > /etc/resolv.conf # to allow internet connection
apt update
apt-get install dnsutils -y
echo nameserver 10.62.2.2 > /etc/resolv.conf    # to run the ping the YudhistiraDNSMaster
```
-  We are then able to test it by running `host -t PTR 10.62.2.2`

## Number 6
```
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
```
Solution:<br>
![yudhistira.named.conf.local](https://cdn.discordapp.com/attachments/824131614073683968/1161819515810750504/image0.jpg?ex=6539afbe&is=65273abe&hm=722c8162f1c8f0c61f90b2b8231bcbf95ab80991e7cb96f5cf8a72cc7bcc3ca1&)

![werkudara.named.conf.local](https://cdn.discordapp.com/attachments/824131614073683968/1161819654302470214/image0.jpg?ex=6539afdf&is=65273adf&hm=30d6a7172fec4476508e0cc35a50129faf77e386a7b4402b4c9b55b528ba9e0c&)

![werkudara.restart.bind](https://cdn.discordapp.com/attachments/824131614073683968/1161821549171581038/IMG_0646.png?ex=6539b1a2&is=65273ca2&hm=ef421fd6bb6934221db3bbb3125d1a2bd57944d1cb24be6ff231c7724e197f6d&)

![nakulaclient.test](https://cdn.discordapp.com/attachments/824131614073683968/1161821700963434496/image0.jpg?ex=6539b1c7&is=65273cc7&hm=c0f7162cc4960df6744997119c4651cc0d2385595840a6385685654506481cb5&)

Explanation:
- First we edit `abimanyu.I07.com` zone in the `named.conf.local` file in YudhistiraDNSMaster adding
```sh  
notify yes;
also-notify { 10.62.2.3; }; // IP WerkudaraDNSSlave
also-transfer { 10.62.2.3; }; // IP WerkudaraDNSSlave
```
- After this we reload bind9 with the command `service bind9 restart`
- We then edit the `named.conf.local` file in `WerkudaraDNSSlave` adding
```sh
zone "abimanyu.I07.com"  {
  type slave;
  masters { 10.62.2.2; }; // IP YudhistiraDNSMaster
  file "/var/lib/bind/abimanyu.I07.com"
}  
```
- Then we restart the bind9 with the command `service bind9 restart` in `WerkudaraDNSSlave`
- In order to test we go to `NakulaClient` and edit the `resolv.conf` file with the command `nano /etc/resolv.conf` and add
```sh
nameserver 10.62.2.2 // IP YudhistiraDNSMaster
nameserver 10.62.2.3 // IP WerkudaraDNSSlave
```
- Then we stop bind9 in `YudhistiraDNSMaster` with the command `service bind9 stop`
- And finally we run `ping abimanyu.I07.com -c 5` in `NakulaClient`

## Number 7
```
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
```
Solution:<br>

```sh
```

Explanation:

## Number 8
```
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.
```
Solution:<br>

Explanation:

## Number 9
```
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
```
Solution:<br>

```sh
```

Explanation:

## Number 10
```
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003
```

Solution:<br>

```sh
```

Explanation:

### Number 11
```
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy
```

Solution:<br>

```sh
```

### Number 12
```
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.
```

Solution:<br>

```sh
```

### Number 13
```
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy
```

Solution:<br>

```sh
```

### Number 14
```
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).
```

Solution:<br>

```sh
```

### Number 15
```
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.
```

Solution:<br>

```sh
```

### Number 16
```
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js 
```

Solution:<br>

```sh
```

### Number 17
```
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.
```

Solution:<br>

```sh
```

### Number 18
```
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.
```

Solution:<br>

```sh
```

### Number 19
```
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)
```

Solution:<br>

```sh
```

### Number 20
```
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.
```

Solution:<br>

```sh
```
