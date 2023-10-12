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
}; 
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

![yudhistira.abimanyu.I07.com](https://media.discordapp.net/attachments/824131614073683968/1161854529055244299/Screenshot_2023-10-11_175712.png?ex=6539d059&is=65275b59&hm=64d7854d7948c1e84264782c3b2b241fed3d76751c22042bbac93db80ffb2afa&=)

![yudhistira.named.conf.local](https://media.discordapp.net/attachments/824131614073683968/1161854457957580921/Screenshot_2023-10-11_175629.png?ex=6539d048&is=65275b48&hm=56cb27a543e282d91e29731f9734795c7889d2dcdc528a692c704760a74e420d&=)

![yudhistira.named.conf.options](https://media.discordapp.net/attachments/824131614073683968/1161854312515899433/Screenshot_2023-10-11_175505.png?ex=6539d026&is=65275b26&hm=026b98b6b802eeadba37935b4ea77a52099d78a896ccc8233126cb584ada42e0&=)

![werkudara.named.conf.local](https://cdn.discordapp.com/attachments/824131614073683968/1161854270459625563/Screenshot_2023-10-11_175432.png?ex=6539d01c&is=65275b1c&hm=cb2141cfd0dc26dacf35b6e4eb48cff967595d643574ce7c784b0ad5bd02bbdd&)

![werkudara.named.conf.options](https://media.discordapp.net/attachments/824131614073683968/1161854346435231765/Screenshot_2023-10-11_175557.png?ex=6539d02e&is=65275b2e&hm=a63e670436d96c5b18a138ba4dd0936664b0fcf063513b204d908cedace61b18&=)

![werkudara.baratayuda.abimanyu.I07.com](https://media.discordapp.net/attachments/824131614073683968/1161854237387538432/Screenshot_2023-10-11_175325.png?ex=6539d014&is=65275b14&hm=53983026ffac78c7d1d5cabafa46e099f08aaeb60f3391575c36a27c033ef43e&=)

![nakulaclient.test](https://media.discordapp.net/attachments/824131614073683968/1161854553063432272/Screenshot_2023-10-11_175100.png?ex=6539d05f&is=65275b5f&hm=efc240eaa70675125d76ad05358f1e0e272ae5ffabcad9bc9d67df4645d58c94&=)

Explanation:
- First we edit the `abimanyu.I07.com` file in `YudhistiraDNSMaster` adding
```sh
nsl         IN      A       10.62.2.3       ; IP WerkudaraDNSSlave
baratayuda  IN      NS      nsl
```
- Then we edit `named.conf.options` in `YudhistiraDNSMaster`, commenting `dnssec-validation auto;` and adding `allow-query{any;};` in the next line.
- We do not alter `named.conf.local` in `YudhistiraDNSMaster` as it has already been properly set up.
- We then edit `named.conf.local` in `WerkudaraDNSSlave` adding
```sh
zone "baratayuda.abimanyu.I07.com"  {
    type master;
    file "/etc/bind/delegasi/baratayuda.abimanyu.I07.com";
};
```
- We also edit `named.conf.options` in `WerkudaraDNSSlave`, commenting `dnssec-validation auto;` and adding `allow-query{any;};` in the next line.
- We then make the directory `delegasi` with in the `/etc/bind/` directory with the command `mkdir /etc/bind/delegasi` and copy `db.local` there and renaming it `baratayuda.abimanyu.I07.com` with the command `cp /etc/bind/db.local /etc/bind/delegasi/baratayuda.abimanyu.I07.com`
- We then edit `baratayuda.abimanyu.I07.com`, changing `.local.` and `root.localhost.` to `baratayuda.abimanyu.I07.com` and `root.baratayuda.abimanyu.I07.com.`
- We also add
```sh
@           IN      NS      baratayuda.abimanyu.I07.com.
@           IN      A       10.62.2.3       ; IP WerkudaraDNSSlave
www         IN      CNAME   baratayuda.abimanyu.I07.com.
@           IN      A       10.62.2.3       ; IP WerkudaraDNSSlave
```
to `baratayuda.abimanyu.I07.com`
- Afterwards we reload bind9 in both clients by running `service bind9 restart` in both `YudhistiraDNSMaster` and `WerkudaraDNSSlave`.
- To test it, we go to `NakulaClient` and run `ping www.baratayuda.abimanyu.I07.com -c  5`

## Number 8
```
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.
```
Solution:<br>
![werkudara.baratayuda.abimanyu.I07.com](https://cdn.discordapp.com/attachments/824131614073683968/1161854639298322523/Screenshot_2023-10-11_181953.png?ex=6539d074&is=65275b74&hm=3e6d2414c0eaa4b035e5877fc413343cd321c21939fd6c39c287591762a6350d&)

![nakulaclient.test](https://media.discordapp.net/attachments/824131614073683968/1161854670390689842/Screenshot_2023-10-11_181753.png?ex=6539d07b&is=65275b7b&hm=5c83a7c334e68f5fd43fb4c009af85a3cee0ed1c4ec9aad0043c8ad19d7f3c6d&=)

Explanation:
- We first edit `baratayuda.abimanyu.I07.com` in `WerkudaraDNSSlave` adding
```sh
rtp         IN      A       10.62.2.3       ; IP WerkudaraDNSSlave
```
- To test it, we go to `NakulaClient` and run `ping rtp.baratayuda.abimanyu.I07.com -c  5`

## Number 9
```
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
```
Solution:<br>

![.bashc](https://media.discordapp.net/attachments/824131614073683968/1161854980588838942/Screenshot_2023-10-11_191701.png?ex=6539d0c5&is=65275bc5&hm=1eaae1014cc91e422b1fb674f0f8b5d3d1efcfe86649fa704600ddf0b1edbec5&=)

![index.php](https://media.discordapp.net/attachments/824131614073683968/1161854826188124200/Screenshot_2023-10-11_191731.png?ex=6539d0a0&is=65275ba0&hm=77586f9309be2bb1217f56067aeaa0cd906c4b94b7efb99767828e9852190b7c&=)

![jarkomm](https://media.discordapp.net/attachments/824131614073683968/1161854857209192448/Screenshot_2023-10-11_191750.png?ex=6539d0a8&is=65275ba8&hm=53f13e5492df8c21fd23cec99211b71fc5af0889c0dd246a6d9e63b33ebba7c2&=)

![abimanyuwebserver.text](https://media.discordapp.net/attachments/824131614073683968/1161854980588838942/Screenshot_2023-10-11_191701.png?ex=6539d0c5&is=65275bc5&hm=1eaae1014cc91e422b1fb674f0f8b5d3d1efcfe86649fa704600ddf0b1edbec5&=)

Explanation:
- We first edit the `.bashrc` using the command `nano ~/.bashrc` and add
```sh
apt update
apt install nginx -y
apt install nginx php php-fpm -y
php -v

nginx -t # To check if configuration is properly done once completed
```
- Then we make the directory `jarkom` in `/var/www` with the command ` mkdir /var/www/jarkom`
- Then we make an `index.php` file there using the command `nano /var/www/jarkom/index.php` and insert 
```php
 <?php
 echo "Halo, Kamu berada di AbimanyuWebServer"; // Name is based on whicheve client the index is made
 ?>
```
- We then make a file `jarkomm` in the directory `/etc/nginx/sites-available` with the command `nano /etc/nginx/sites-available/jarkomm` and insert
```sh
 server {

 	listen 80;

 	root /var/www/jarkom;

 	index index.php index.html index.htm;
 	server_name _;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }
```
- Then we make a symlink with the command  `ln -s /etc/nginx/sites-available/jarkomm /etc/nginx/sites-enabled`
- Afterwards we reload nginx with the commnad `service nginx restart`
- We can test if the congiguration is correct with the command `nginx -t` but not in this case as it is in the `.bashrc` file so it will be run whenever the machine is restarted

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
