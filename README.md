# Jarkom-Modul-1-F02-2022

Anggota:
1. Eldenabih Tavirazin Lutvie (5025201213)
2. Nouval Bachrezi (502520...)
3. Marsa A R (...)

## Soal 1
WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet (1). 

Konfigurasi dan command pada terminal nya adalah sebagai berikut.

### Ostania
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.29.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.29.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.29.3.1
	netmask 255.255.255.0
```
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.29.0.0/16

cat /etc/resolv.conf
```


### SSS
```
auto eth1
iface eth1 inet static
	address 10.29.1.2
	netmask 255.255.255.0
	gateway 10.29.1.1
```
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Garden
```
auto eth1
iface eth1 inet static
	address 10.29.1.3
	netmask 255.255.255.0
	gateway 10.29.1.1
```
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### WISE
```
auto eth0
iface eth0 inet static
	address 10.29.3.2
	netmask 255.255.255.0
	gateway 10.29.3.1
```
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Berlint
```
auto eth0
iface eth0 inet static
	address 10.29.2.2
	netmask 255.255.255.0
	gateway 10.29.2.1
```
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Eden
```
auto eth0
iface eth0 inet static
	address 10.29.2.3
	netmask 255.255.255.0
	gateway 10.29.2.1
```
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```
<br>

```
ping google.com -c 5
```

![image](https://user-images.githubusercontent.com/85897222/198836665-cd9299cd-69ce-4887-8a16-887ec4e09b57.png) <br>
![image](https://user-images.githubusercontent.com/85897222/198836836-0b445f58-53e2-4480-b1d1-3cdbaa1456d1.png) <br>


## Soal 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise (2).

Melakukan command-command seperti berikut.

### WISE
```
apt-get update
apt-get install bind9 -y
```
```
nano /etc/bind/named.conf.local
```
```
zone "wise.f01.com" {
    type master;
    file "/etc/bind/wise/wise.f01.com";
};
```
```
mkdir /etc/bind/wise

cp /etc/bind/db.local /etc/bind/wise/wise.f01.com

nano /etc/bind/wise/wise.f01.com
```
```
;
;BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.f01.com. root.wise.f01.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.f01.com.
@       IN      A       10.29.3.2
@       IN      AAAA    ::1
```
```
service bind9 restart
```
```
nano /etc/bind/named.conf.local
```
```
zone "wise.f01.com" {
    type master;
    file "/etc/bind/wise/wise.f01.com";
};
```
```
service bind9 restart
```
### Berlint
```
apt-get update
apt-get install bind9 -y
```
```
nano /etc/bind/named.conf.local
```
```
zone "wise.f01.com" {
    type slave;
    masters { 10.29.3.2; };
    file "/var/lib/bind/wise.f01.com";
};
```
```
service bind9 restart
```
### Garden
```
nano /etc/resolv.conf
```
```
nameserver 10.29.3.2
```
```
ping wise.f01.com
```
### SSS
```
nano /etc/resolv.conf
```
```
nameserver 10.29.3.2
```
```
ping wise.f01.com
```

![image](https://user-images.githubusercontent.com/85897222/198836988-13106d2e-a0c8-46f6-963f-1e8464ecbe05.png)


## Soal 3

Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden (3).

Melakukan command-command seperti berikut.

### WISE
```
nano /etc/bind/wise/wise.f01.com
```
tambahkan:
```
eden  IN   A  10.29.2.2
```
```
service bind9 restart
```
### SSS
```
ping eden.wise.f01.com -c 5
```

### Garden
```
ping eden.wise.f01.com -c 5
```

![image](https://user-images.githubusercontent.com/85897222/198837132-a62a6fc5-5e73-4749-86ab-8f2a4416ac23.png)


## Soal 4
Buat juga reverse domain untuk domain utama (4)

Melakukan command-command seperti berikut.

### WISE
```
nano /etc/bind/named.conf.local
```
```
zone "wise.f01.com" {
        type master;
        file "/etc/bind/wise/wise.f01.com";
};

zone "3.29.10.in-addr.arpa" {
        type master;
        file "/etc/bind/wise/3.29.10.in-addr.arpa";
};
```
```
cp /etc/bind/db.local /etc/bind/wise/2.3.29.10.in-addr.arpa
nano /etc/bind/wise/3.29.10.in-addr.arpa
```
```
;
;BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.f01.com. root.wise.f01.com. (
                                2       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
3.29.10.in-addr.arpa.   IN      NS      wise.f01.com.
2                       IN      PTR     wise.f01.com.
```
```
service bind9 restart
```
### Garden
```
apt-get update
apt-get install bind9 -y
```
```
host -t PTR 10.29.3.2
```
### SSS
```
apt-get update
apt-get install bind9 -y
```
```
host -t PTR 10.29.3.2
```

![image](https://user-images.githubusercontent.com/85897222/198839504-aa733a3a-e8f4-4c39-b95d-c42358cbd11a.png)


## Soal 5
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama (5).

### WISE
```
nano /etc/bind/named.conf.local
```
```
zone "wise.f01.com" {
    type master;
    notify yes;
    also-notify { 10.29.2.2; };
    allow-transfer { 10.29.2.2; };
    file "/etc/bind/wise/wise.f01.com";
};

zone "2.3.29.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/2.3.29.10.in-addr.arpa";
};
```
```
service bind9 restart
```
### Berlint
```
apt-get update
apt-get install bind9 -y
```
```
nano /etc/bind/named.conf.local
```
```
zone "wise.f01.com" {
	type slave;
	masters { 10.29.3.2; };
	file "/var/lib/bind/wise.f01.com";
};
```
```
service bind9 restart
```

## Kendala
Masih belum terbiasa menggunakan shell scripting.
