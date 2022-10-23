### TP3 : On va router des trucs

on à changer les adresses IP des 2  machines
## I. ARP
# 1. Echange ARP

🌞_Générer des requêtes ARP:_


ping 10.3.1.11 depuis marcel(10.3.1.12)

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

ping 10.3.1.12 depuis john(10.3.1.11)

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

de john
[melanie@localhost ~]$ ip neigh show
10.3.1.12 dev enp0s8 lladdr 08:00:27:6d:5b:bd STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 DELAY

de marcel
[melanie@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 DELAY
10.3.1.11 dev enp0s8 lladdr 08:00:27:9d:ab:48 STALE

de marcel à john
[melanie@localhost ~]$ ip neigh show 10.3.1.11
10.3.1.11 dev enp0s8 lladdr 08:00:27:9d:ab:48 STALE

de john à marcel 
[melanie@localhost ~]$ ip neigh show 10.3.1.12
10.3.1.12 dev enp0s8 lladdr 08:00:27:6d:5b:bd STALE


adresse mac de John: 08:00:27:9d:ab:48
adresse mac de marcel: 08:00:27:6d:5b:bd

voir mac de marcel
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

voir mac de john 
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





2. Analyse de trames
🌞Analyse de trames

marcel
7900 packets captured
7905 packets received by filter
0 packets dropped by kernel

john
5788 packets captured
5790 packets received by filter
0 packets dropped by kernel

john
[melanie@localhost ~]$ sudo ip neigh flush all
[melanie@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 REACHABLE

marcel
[melanie@localhost ~]$ sudo ip neigh flush all
[melanie@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 REACHABLE


voir capture ici [ARP](./capture%20r%C3%A9seau.pcapng)

II. Routage
1. Mise en place du routage
🌞Activer le routage sur le noeud router

activation rotage
$ sudo firewall-cmd --list-all
$ sudo firewall-cmd --get-active-zone

# Activation du masquerading
$ sudo firewall-cmd --add-masquerade --zone=public
$ sudo firewall-cmd --add-masquerade --zone=public --permanent

(voir memo activation du routage)

🌞Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping:
sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s8 depuis Marcel(10.3.2.12) sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s8 depuis John(10.3.1.11)

john
[melanie@localhost ~]$ ip route show
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 100
10.3.2.0/24 via 10.3.1.254 dev enp0s8

marcel
[melanie@localhost ~]$ ip route show
10.3.1.0/24 via 10.3.2.254 dev enp0s8
10.3.2.0/24 dev enp0s8 proto kernel scope link src 10.3.2.12 metric 100

ping depuis john
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

ping depuis marcel
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

2. Analyse de trames
🌞Analyse des échanges ARP

sudo ip neigh flush all depuis Marcel et John et routeur

ping depuis john 10.3.2.12
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

john
[melanie@localhost ~]$ ip neigh show
10.3.1.254 dev enp0s8 lladdr 08:00:27:a5:0e:93 STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:07 DELAY

marcel
[melanie@localhost ~]$ ip neigh show
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.2.254 dev enp0s8 lladdr 08:00:27:73:23:b7 STALE

routeur
10.3.1.11 dev enp0s8 lladdr 08:00:27:9d:ab:48 STALE
10.3.2.12 dev enp0s9 lladdr 08:00:27:6d:5b:bd STALE


| ordre | type trame  | IP source | MAC source                 | IP destination | MAC destination            |
|-------|-------------|-----------|-------------------------   |----------------|----------------------------|
| 1     | Requête ARP | x         | john `08:00:27:9d:ab:48`   | x              | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | x         | marcel `08:00:27:6d:5b:bd` | x              | john `08:00:27:9d:ab:48`   |
| 3     | Ping        | 10.3.2.254| john `08:00:27:9d:ab:48`   | 10.3.2.12      | marcel `08:00:27:6d:5b:bd` |
| 4     | Pong        | 10.3.2.12 | marcel `08:00:27:6d:5b:bd` | 10.3.2.254     | john `08:00:27:9d:ab:48`   |




récupérer un fichier
scp melanie@10.3.2.12:~/routage_marcel.pcap .
voir capture [ici](./tp3_routage_marcel.pcap)

3. Accès internet
🌞Donnez un accès internet à vos machines

