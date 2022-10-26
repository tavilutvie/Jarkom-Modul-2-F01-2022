# Jarkom-Modul-1-F01-2022|

## Soal 1
WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet (1). 

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

## Soal 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise (2).

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
nano /etc/bind/named.conf.options
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
## Soal 3
```
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden (3).

### WISE
```
nano /etc/bind/wise/wise.f01.com
```
```
;
;BIND data file for local loopback interface
;
eden  IN   A  10.29.2.2
```
```
service bind9 restart
```
### SSS
```
ping eden.wise.f01.com
```
### Garden
```
ping eden.wise.f01.com
```
## Soal 4
Buat juga reverse domain untuk domain utama (4)
```
