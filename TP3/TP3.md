# TP3 : On va router des trucs

Au menu de ce TP, on va revoir un peu ARP et IP histoire de se mettre en jambes dans un environnement avec des VMs.
Puis on mettra en place un routage simple, pour permettre à deux LANs de communiquer.

(on à changer les adresses IP des 2  machines)

## I. ARP
### 1. Echange ARP

🌞 __Générer des requêtes ARP:__

<br/>

depuis marcel (10.3.1.12)

```ping 10.3.1.11```

```
[melanie@localhost ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=64 time=1.52 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=64 time=1.16 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=64 time=0.637 ms
64 bytes from 10.3.1.11: icmp_seq=4 ttl=64 time=0.625 ms
64 bytes from 10.3.1.11: icmp_seq=5 ttl=64 time=1.05 ms
64 bytes from 10.3.1.11: icmp_seq=6 ttl=64 time=0.773 ms
^C
--- 10.3.1.11 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5068ms
```
depuis John (10.3.1.11):

```ping 10.3.1.12```

```
[melanie@localhost ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=1.36 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=1.08 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=1.09 ms
64 bytes from 10.3.1.12: icmp_seq=4 ttl=64 time=1.16 ms
64 bytes from 10.3.1.12: icmp_seq=5 ttl=64 time=1.29 ms
64 bytes from 10.3.1.12: icmp_seq=6 ttl=64 time=0.657 ms
^C
--- 10.3.1.12 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5007ms
rtt min/avg/max/mdev = 0.657/1.104/1.359/0.226 ms
```
Les pings s'effectuent correctement, il y a une connection entre les deux.

__.__ Voir les tables ARP:

De john:
```
[melanie@localhost ~]$ ip neigh show
10.3.1.12 dev enp0s8 lladdr 08:00:27:6d:5b:bd STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 DELAY
```

De marcel:
```
[melanie@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 DELAY
10.3.1.11 dev enp0s8 lladdr 08:00:27:9d:ab:48 STALE
```

De marcel à john:
```
[melanie@localhost ~]$ ip neigh show 10.3.1.11
10.3.1.11 dev enp0s8 lladdr 08:00:27:9d:ab:48 STALE
```

De john à marcel :
```
[melanie@localhost ~]$ ip neigh show 10.3.1.12
10.3.1.12 dev enp0s8 lladdr 08:00:27:6d:5b:bd STALE
```

adresse mac de John: 08:00:27:9d:ab:48

adresse mac de marcel: 08:00:27:6d:5b:bd

__.__ Voir mac de marcel:
```
[melanie@localhost ~]$ ifconfig -a
enp0s8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.3.1.12  netmask 255.255.255.0  broadcast 10.3.1.255
        inet6 fe80::a00:27ff:fe6d:5bbd  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:6d:5b:bd  txqueuelen 1000  (Ethernet)
        RX packets 13754  bytes 844736 (824.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 26989  bytes 7316906 (6.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

__.__ Voir mac de john :
```
[melanie@localhost ~]$ ifconfig -a
enp0s8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.3.1.11  netmask 255.255.255.0  broadcast 10.3.1.255
        inet6 fe80::a00:27ff:fe9d:ab48  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:9d:ab:48  txqueuelen 1000  (Ethernet)
        RX packets 143  bytes 12949 (12.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 117  bytes 13317 (13.0 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```




### 2. Analyse de trames
🌞 __Analyse de trames:__

utilisez la commande tcpdump pour réaliser une capture de trame

depuis marcel
```
7900 packets captured
7905 packets received by filter
0 packets dropped by kernel
```

depuis john:
```
5788 packets captured
5790 packets received by filter
0 packets dropped by kernel
```
__.__ Videz vos tables ARP, sur les deux machines, puis effectuez un ping

depuis john:
```
[melanie@localhost ~]$ sudo ip neigh flush all
```
```
[melanie@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 REACHABLE
```

depuis marcel:
```
[melanie@localhost ~]$ sudo ip neigh flush all
```
```
[melanie@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 REACHABLE
```
🦈
voir capture réseau [ARP](./capture%20r%C3%A9seau.pcapng)

## II. Routage
### 1. Mise en place du routage

🌞 __Activer le routage sur le noeud router:__

activation routage
```
$ sudo firewall-cmd --list-all
```
```
$ sudo firewall-cmd --get-active-zone
```
#### Activation du masquerading
```
$ sudo firewall-cmd --add-masquerade --zone=public
```
```
$ sudo firewall-cmd --add-masquerade --zone=public --permanent
```

(voir memo activation du routage)

🌞 __Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping:__

depuis marcel (10.3.2.12):
```
sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s8 
``` 
depuis John (10.3.1.11):
```
sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s8
```
__.__ Vérifier que les routes ont bien été formés:

depuis john :
```
[melanie@localhost ~]$ ip route show
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 100
10.3.2.0/24 via 10.3.1.254 dev enp0s8
```
depuis marcel:
```
[melanie@localhost ~]$ ip route show
10.3.1.0/24 via 10.3.2.254 dev enp0s8
10.3.2.0/24 dev enp0s8 proto kernel scope link src 10.3.2.12 metric 100
```
__.__ On vérifie que les deux macines puissent se joindre:

ping depuis john
```
[melanie@localhost ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=1.14 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=2.08 ms
64 bytes from 10.3.2.12: icmp_seq=3 ttl=63 time=2.02 ms
64 bytes from 10.3.2.12: icmp_seq=4 ttl=63 time=1.88 ms
64 bytes from 10.3.2.12: icmp_seq=5 ttl=63 time=1.90 ms
64 bytes from 10.3.2.12: icmp_seq=6 ttl=63 time=1.96 ms
^C
--- 10.3.2.12 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5026ms
rtt min/avg/max/mdev = 1.135/1.827/2.077/0.321 ms
```

ping depuis marcel
```
[melanie@localhost ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=63 time=1.27 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=63 time=1.75 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=63 time=1.83 ms
64 bytes from 10.3.1.11: icmp_seq=4 ttl=63 time=2.29 ms
64 bytes from 10.3.1.11: icmp_seq=5 ttl=63 time=2.29 ms
64 bytes from 10.3.1.11: icmp_seq=6 ttl=63 time=2.05 ms
^C
--- 10.3.1.11 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5019ms
rtt min/avg/max/mdev = 1.272/1.913/2.291/0.355 ms
```

### 2. Analyse de trames
🌞 __Analyse des échanges ARP:__

__.__ Videz les tables ARP de John, Marcel et routeur:

```sudo ip neigh flush all ```

ping depuis john 10.3.2.12
```
[melanie@localhost ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=2.57 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=2.02 ms
64 bytes from 10.3.2.12: icmp_seq=3 ttl=63 time=2.06 ms
64 bytes from 10.3.2.12: icmp_seq=4 ttl=63 time=2.12 ms
64 bytes from 10.3.2.12: icmp_seq=5 ttl=63 time=2.36 ms
64 bytes from 10.3.2.12: icmp_seq=6 ttl=63 time=2.17 ms
^C
--- 10.3.2.12 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5014ms
rtt min/avg/max/mdev = 2.016/2.214/2.571/0.194 ms
```

table ARP de john:
```
[melanie@localhost ~]$ ip neigh show
10.3.1.254 dev enp0s8 lladdr 08:00:27:a5:0e:93 STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 DELAY
```

table ARP de marcel:
```
[melanie@localhost ~]$ ip neigh show
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.2.254 dev enp0s8 lladdr 08:00:27:73:23:b7 STALE
```

table ARP de routeur:
```
10.3.1.11 dev enp0s8 lladdr 08:00:27:9d:ab:48 STALE
10.3.2.12 dev enp0s9 lladdr 08:00:27:6d:5b:bd STALE
```

| ordre | type trame  | IP source | MAC source                 | IP destination | MAC destination            |
|-------|-------------|-----------|-------------------------   |----------------|----------------------------|
| 1     | Requête ARP | x         | john `08:00:27:9d:ab:48`   | x              | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | x         | marcel `08:00:27:6d:5b:bd` | x              | john `08:00:27:9d:ab:48`   |
| 3     | Ping        | 10.3.2.254| john `08:00:27:9d:ab:48`   | 10.3.2.12      | marcel `08:00:27:6d:5b:bd` |
| 4     | Pong        | 10.3.2.12 | marcel `08:00:27:6d:5b:bd` | 10.3.2.254     | john `08:00:27:9d:ab:48`   |





(récupérer un fichier de capture tcpdump:

scp melanie@10.3.2.12:~/routage_marcel.pcap .)

🦈 
voir capture [ici](./tp3_routage_marcel.pcap)

### 3. Accès internet
🌞 __Donnez un accès internet à vos machines:__

Ajoutez une carte NAT en 3ème inteface sur le router pour qu'il ait un accès internet

Ajouter une route par défaut (temporairement) depuis john (10.3.2.254):
```
sudo ip route add default via 10.3.1.254 dev enp0s8 
```

pour Marcel(10.3.2.12):
```
sudo ip route add default via 10.3.2.254 dev enp0s8 
```

test de la connexion:
```
[melanie@localhost ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=53 time=24.2 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=53 time=25.3 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=53 time=39.1 ms
64 bytes from 1.1.1.1: icmp_seq=4 ttl=53 time=27.4 ms
64 bytes from 1.1.1.1: icmp_seq=5 ttl=53 time=31.1 ms
^C
--- 1.1.1.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 24.240/29.439/39.121/5.381 ms
```
__.__ Vérifiez que vous avez une résolution de noms qui fonctionne avec dig:

```
[melanie@localhost ~]$ dig gitlab.com

; <<>> DiG 9.11.36-RedHat-9.11.36-3.el8_6.1 <<>> gitlab.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58229
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;gitlab.com.                    IN      A

;; ANSWER SECTION:
gitlab.com.             63      IN      A       172.65.251.78

;; Query time: 33 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Mon Oct 24 11:58:27 CEST 2022
;; MSG SIZE  rcvd: 55
```



🌞 __Analyse de trames:__

Effectuez un ping 8.8.8.8 depuis john

```sudo tcpdump -i enp0s8 -c 10 -w tp3_routage_internet.pcap not port 22 | ping 8.8.8.8```


| ordre | type trame | IP source          | MAC source              | IP destination | MAC destination |
|-------|------------|--------------------|-------------------------|----------------|-----------------|
| 1     | ping       | john 10.3.1.11     | john 08:00:27:b1:c7:51  | 8.8.8.8      |08:00:27:34:bf:c8| 
| 2     | pong       | 8.8.8.8            | 08:00:27:34:bf:c8       | john 10.3.1.11 | john 08:00:27:b1:c7:51| 


🦈 **Capture réseau**
[ici](./tp3_routage_internet.pcap) 


## III. DHCP

### 1. Mise en place du serveur DHCP

🌞 __Sur la machine john, vous installerez et configurerez un serveur DHCP:__

depuis John: 
 installation du serveur
```
sudo dnf install dhcp-server  
```
configuration
```
[melanie@localhost ~]$ sudo nano /etc/dhcp/dhcpd.conf 
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
default-lease-time 900;
max-lease-time 10800;

authoritative;

subnet 10.3.1.0 netmask 255.255.255.0 {
range 10.3.1.1 10.3.1.254;
option routers 10.3.1.254;
option subnet-mask 255.255.255.0;
option domain-name-servers 1.1.1.1;
}
```

```
[melanie@localhost ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; vendor preset: disabled)
     Active: active (running) since Mon 2022-10-24 11:22:24 CEST; 15min ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1289 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 5906)
     Memory: 4.7M
        CPU: 9ms
     CGroup: /system.slice/dhcpd.service
             └─1289 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Oct 24 11:22:24 localhost.localdomain systemd[1]: Started DHCPv4 Server Daemon.
Oct 24 11:26:09 localhost.localdomain dhcpd[1289]: DHCPDISCOVER from 08:00:27:d9:79:3d via enp0s8
Oct 24 11:26:09 localhost.localdomain dhcpd[1289]: ICMP Echo reply while lease 10.3.1.1 valid.
Oct 24 11:26:09 localhost.localdomain dhcpd[1289]: Abandoning IP address 10.3.1.1: pinged before offer
Oct 24 11:26:11 localhost.localdomain dhcpd[1289]: DHCPDISCOVER from 08:00:27:d9:79:3d via enp0s8
Oct 24 11:26:12 localhost.localdomain dhcpd[1289]: DHCPOFFER on 10.3.1.2 to 08:00:27:d9:79:3d via enp0s8
Oct 24 11:26:12 localhost.localdomain dhcpd[1289]: DHCPREQUEST for 10.3.1.2 (10.3.1.11) from 08:00:27:d9:79:3d via enp0s8
Oct 24 11:26:12 localhost.localdomain dhcpd[1289]: DHCPACK on 10.3.1.2 to 08:00:27:d9:79:3d via enp0s8
```

__.__ Création de la machine Bob + configuration:
```
[melanie@localhost ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s8 

DEVICE=enp0s8

BOOTPROTO=dhcp
ONBOOT=yes
```

```
[melanie@localhost ~]$ sudo nmcli con up "System enp0s8"

Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/2)
```

ip a
```
[melanie@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:d9:79:3d brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.2/24 brd 10.3.1.255 scope global dynamic noprefixroute enp0s8
       valid_lft 863sec preferred_lft 863sec
    inet6 fe80::a00:27ff:fed9:793d/64 scope link 
       valid_lft forever preferred_lft forever
```

🌞 __Améliorer la configuration du DHCP:__

sudo nano /etc/dhcp/dhcpd.conf
```
option routers 10.3.1.254;
option subnet-mask 255.255.255.0;
option domain-name-servers 1.1.1.1;
```
route par défaut est option routers 10.3.1.254;

```
[melanie@localhost ~]$ sudo nmcli con down "System enp0s8"
```
```
[melanie@localhost ~]$ sudo nmcli con up "System enp0s8"
```
```
PING 10.3.1.254 (10.3.1.254) 
56(84) bytes of data.
64 bytes from 10.3.1.254: icmp_seq=1 ttl=64 time=0.423 ms
64 bytes from 10.3.1.254: icmp_seq=2 ttl=64 time=0.539 ms
64 bytes from 10.3.1.254: icmp_seq=3 ttl=64 time=0.452 ms
64 bytes from 10.3.1.254: icmp_seq=4 ttl=64 time=0.297 ms
^C
--- 10.3.1.254 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3113ms
```

```
[melanie@localhost ~]$ ip r s
default via 10.3.1.254 dev enp0s8 proto dhcp src 10.3.1.2 metric 100 
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.2 metric 100 
```

```
[melanie@localhost ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=61 time=21.5 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=61 time=22.9 ms
^C
--- 1.1.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 21.475/22.164/22.854/0.689 ms
```

```
[melanie@localhost ~]$ dig google.com

; <<>> DiG 9.16.23-RH <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 4468
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             36      IN      A       142.250.179.110

;; Query time: 24 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mon Oct 24 12:37:33 CEST 2022
;; MSG SIZE  rcvd: 55
```

```
[melanie@localhost ~]$ ping google.com
PING google.com (216.58.198.206) 56(84) bytes of data.
64 bytes from par10s27-in-f206.1e100.net (216.58.198.206): icmp_seq=1 ttl=61 time=29.9 ms
64 bytes from par10s27-in-f206.1e100.net (216.58.198.206): icmp_seq=2 ttl=61 time=22.4 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 22.392/26.127/29.863/3.735 ms
```
### 2. Analyse de trames

🌞 __Analyse de trames:__

```sudo tcpdump -i enp0s8 -w tp3_dhcp.pcap not port 22 sudo dhclient ```

🦈 Capture réseau [ici](./tp3_dhcp.pcap)

Source Address: 0.0.0.0

Destination Address: 255.255.255.255

Client IP address: 0.0.0.0

