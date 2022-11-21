# Sujet Réseau et Infra


## I. Dumb switch

1. __Topologie 1__




### __3. Setup topologie 1__

pour voir ip address - sh
modifier ip: ip 10.5.1.1/24

```
PC1> sh

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
PC1    0.0.0.0/0            0.0.0.0           00:50:79:66:68:00  20008  127.0.0.1:20009
       fe80::250:79ff:fe66:6800/64
```

```
PC1> 1
PC1> ip 10.5.1.1/24
Checking for duplicate address...
PC1 : 10.5.1.1 255.255.255.0
```

```
PC1> sh

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
PC1    10.5.1.1/24          0.0.0.0           00:50:79:66:68:00  20008  127.0.0.1:20009
       fe80::250:79ff:fe66:6800/64
```

```
PC2> ip 10.5.1.2/24
Checking for duplicate address...
PC2 : 10.5.1.2 255.255.255.0
```

```
PC2> sh

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
PC2    10.5.1.2/24          0.0.0.0           00:50:79:66:68:01  20010  127.0.0.1:20011
       fe80::250:79ff:fe66:6801/64
```


ping depuis PC1 à PC2
```
PC1> ping 10.5.1.2

84 bytes from 10.5.1.2 icmp_seq=1 ttl=64 time=4.301 ms
84 bytes from 10.5.1.2 icmp_seq=2 ttl=64 time=4.301 ms
84 bytes from 10.5.1.2 icmp_seq=3 ttl=64 time=6.367 ms
84 bytes from 10.5.1.2 icmp_seq=4 ttl=64 time=2.531 ms
84 bytes from 10.5.1.2 icmp_seq=5 ttl=64 time=2.970 ms
^C
```


ping depuis PC2 à PC1
```
PC2> ping 10.5.1.1

84 bytes from 10.5.1.1 icmp_seq=1 ttl=64 time=5.100 ms
84 bytes from 10.5.1.1 icmp_seq=2 ttl=64 time=2.405 ms
84 bytes from 10.5.1.1 icmp_seq=3 ttl=64 time=3.809 ms
84 bytes from 10.5.1.1 icmp_seq=4 ttl=64 time=3.108 ms
84 bytes from 10.5.1.1 icmp_seq=5 ttl=64 time=2.598 ms
^C
```
## II. VLAN

## 1. Topologie 2

## 3. Setup topologie 2

🌞 __Adressage__

définissez les IPs statiques sur tous les VPCS

```
PC3> ip 10.5.10.3/24
Checking for duplicate address...
PC3 : 10.5.10.3 255.255.255.0

PC3> sh

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
PC3    10.5.10.3/24         0.0.0.0           00:50:79:66:68:02  20006  127.0.0.1:20007
       fe80::250:79ff:fe66:6802/64
```

vérifiez avec des ping que tout le monde se ping
```
PC1> ping 10.5.10.2

84 bytes from 10.5.10.2 icmp_seq=1 ttl=64 time=8.420 ms
84 bytes from 10.5.10.2 icmp_seq=2 ttl=64 time=7.713 ms
^C
PC1> ping 10.5.10.3

84 bytes from 10.5.10.3 icmp_seq=1 ttl=64 time=4.231 ms
84 bytes from 10.5.10.3 icmp_seq=2 ttl=64 time=3.955 ms
^C
```

```
PC2> ping 10.5.10.1

84 bytes from 10.5.10.1 icmp_seq=1 ttl=64 time=1.643 ms
84 bytes from 10.5.10.1 icmp_seq=2 ttl=64 time=5.688 ms
^C
PC2> ping 10.5.10.3

84 bytes from 10.5.10.3 icmp_seq=1 ttl=64 time=5.242 ms
84 bytes from 10.5.10.3 icmp_seq=2 ttl=64 time=4.830 ms
^C
```

```
PC3> ping 10.5.10.1

84 bytes from 10.5.10.1 icmp_seq=1 ttl=64 time=4.068 ms
84 bytes from 10.5.10.1 icmp_seq=2 ttl=64 time=4.296 ms
^C
PC3> ping 10.5.10.2

84 bytes from 10.5.10.2 icmp_seq=1 ttl=64 time=3.880 ms
84 bytes from 10.5.10.2 icmp_seq=2 ttl=64 time=2.993 ms
84 bytes from 10.5.10.2 icmp_seq=3 ttl=64 time=3.878 ms
^C
```

🌞 __Configuration des VLANs__

voir mémo cisco ( VLAN Mode access))


🌞 __Vérif__

Le PC3 ne ping plus personne

```
PC3> ping 10.5.10.2

host (10.5.10.2) not reachable
```

PC1 et PC2 peuvent toujours se ping
```
PC2> ping 10.5.10.1

84 bytes from 10.5.10.1 icmp_seq=1 ttl=64 time=1.246 ms
84 bytes from 10.5.10.1 icmp_seq=2 ttl=64 time=0.759 ms
^C
```

## III. Routing

## 1. Topologie 3

sh run -- show running config


## 3. Setup topologie 3

🌞 __Adressage__

pc1
```
PC1> ip 10.5.10.1/24
Checking for duplicate address...
PC1 : 10.5.10.1 255.255.255.0

PC1> sh

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
PC1    10.5.10.1/24         0.0.0.0           00:50:79:66:68:00  20014  127.0.0.1:20015
       fe80::250:79ff:fe66:6800/64
```
pc2
```
PC2> ip 10.5.10.2/24
Checking for duplicate address...
PC2 : 10.5.10.2 255.255.255.0

PC2> sh

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
PC2    10.5.10.2/24         0.0.0.0           00:50:79:66:68:01  20012  127.0.0.1:20013
       fe80::250:79ff:fe66:6801/64
```
adm1
```
adm1> ip 10.5.20.1/24
Checking for duplicate address...
adm1 : 10.5.20.1 255.255.255.0

adm1> sh

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
adm1   10.5.20.1/24         0.0.0.0           00:50:79:66:68:02  20016  127.0.0.1:20017
       fe80::250:79ff:fe66:6802/64
```


🌞 __Configuration des VLANs__


sw1 (Vlan associé)
```
sw1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
sw1(config)#vlan 10
sw1(config-vlan)#name clients
sw1(config-vlan)#exit
sw1(config)#vlan 20
sw1(config-vlan)#name admins
sw1(config-vlan)#exit
sw1(config)#vlan 30
sw1(config-vlan)#name servers
sw1(config-vlan)#exit
sw1(config)#exit
sw1#
*Nov  8 17:25:31.616: %SYS-5-CONFIG_I: Configured from console by console
sw1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/2, Et0/3, Et1/1, Et1/2
                                                Et1/3, Et2/0, Et2/1, Et2/2
                                                Et2/3, Et3/0, Et3/1, Et3/2
                                                Et3/3
10   clients                          active    Et0/0, Et0/1
20   admins                           active    Et1/0
30   servers                          active
```

Attribution à chaque interface du switch d'un vlan

(celles en vlan 10 déjà configuré plus tôt)
```
sw1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
sw1(config)#interface Ethernet0/2
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 20
sw1(config-if)#exit
sw1(config)#interface Ethernet0/3
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 30
sw1(config-if)#exit
sw1(config)#
```

visualisation globale (le câble Et1/0 n'est pas utilisé ici)
```
sw1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et1/1, Et1/2, Et1/3, Et2/0
                                                Et2/1, Et2/2, Et2/3, Et3/0
                                                Et3/1, Et3/2, Et3/3
10   clients                          active    Et0/0, Et0/1
20   admins                           active    Et0/2, Et1/0
30   servers                          active    Et0/3
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
```

il faut ajouter le port qui pointe vers le routeur comme un trunk : c'est un port entre deux équipements réseau (un switch et un routeur)

```
sw1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
sw1(config)#interface Ethernet1/0
sw1(config-if)#switchport trunk encapsulation dot1q
sw1(config-if)#switchport mode trunk
sw1(config-if)#switch
*Nov 10 14:21:55.955: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet1/0, changed state to down
sw1(config-if)#switchport trunk allowed vlan add 10,20,30
sw1(config-if)#exit
sw1(config)#exit
sw1#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et1/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et1/0       1-4094

Port        Vlans allowed and active in management domain
Et1/0       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et1/0       1,10,20,30
```


## ➜ Pour le routeur
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip addr 10.5.10.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.20
R1(config-subif)#
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip addr 10.5.20.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip addr 10.5.30.254 255.255.255.0
R1(config-subif)#exit
```

```

sw1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
sw1(config)#interface Ethernet0/0
sw1(config-if)#switchport trunk encapsulation dot1q
sw1(config-if)#switchport trunk allowed vlan 10,20,30
sw1(config-if)#switchport mode trunk
sw1(config-if)#exit
sw1(config)#
*Nov 20 16:12:36.645: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to down
sw1(config)#exit
*Nov 20 16:12:39.657: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to up
sw1(config)#exit
sw1#
```

```
PC1> ip 10.5.10.1/24 10.5.10.254
Checking for duplicate address...
PC1 : 10.5.10.1 255.255.255.0 gateway 10.5.10.254
```

```
PC2> ip 10.5.10.2/24 10.5.10.254
Checking for duplicate address...
PC2 : 10.5.10.2 255.255.255.0 gateway 10.5.10.254
```

```
adm1>
adm1> ip 10.5.20.1/24 10.5.10.254
Checking for duplicate address...
adm1 : 10.5.10.2 255.255.255.0 gateway 10.5.10.254
```

VM:
```
[melanie@web1 ~] sudo ip route add default via 10.5.30.254
```

```
PC1> ping 10.5.10.254

84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=21.164 ms
84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=5.499 ms
84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=11.248 ms
^C
```
```
PC1> ping 10.5.30.1

10.5.30.1 icmp_seq=1 timeout
84 bytes from 10.5.30.1 icmp_seq=2 ttl=63 time=15.311 ms
84 bytes from 10.5.30.1 icmp_seq=3 ttl=63 time=23.950 ms
84 bytes from 10.5.30.1 icmp_seq=4 ttl=63 time=17.585 ms
^C
```

```
PC2> ping 10.5.10.254

84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=10.332 ms
84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=13.660 ms
84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=7.081 ms
^C
```

```
PC2> ping 10.5.20.254

84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=12.534 ms
84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=8.117 ms
84 bytes from 10.5.20.254 icmp_seq=3 ttl=255 time=11.185 ms
^C
```

```
[melanie@web1 ~]$ ping 10.5.10.1
PING 10.5.10.1 (10.5.10.1) 56(84) bytes of data.
64 bytes from 10.5.10.1: icmp_seq=1 ttl=63 time=18.7 ms
64 bytes from 10.5.10.1: icmp_seq=2 ttl=63 time=25.1 ms
64 bytes from 10.5.10.1: icmp_seq=3 ttl=63 time=22.4 ms
64 bytes from 10.5.10.1: icmp_seq=4 ttl=63 time=36.7 ms
^C
--- 10.5.10.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 18.687/25.700/36.678/6.731 ms
```

# IV. NAT

## 3. Setup topologie 4

### 🌞 __Ajoutez le noeud Cloud à la topo__

côté routeur, il faudra récupérer un IP en DHCP (voir le mémo Cisco)

vous devriez pouvoir ping 1.1.1.1

```(config-if)# ip address dhcp```

```



### 🌞 __Configurez le NAT__
voir mémo cisco NAT

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface fastEthernet 1/0
R1(config-if)#ip nat outside
R1(config-if)#interface fastEthernet 0/0
R1(config-if)#ip nat inside
R1(config-if)#exit
R1(config)#access-list 1 permit any
R1(config)#ip nat inside source list 1 interface fastEthernet 1/0 overload
R1(config)#exit
```

### 🌞 __Test__


# V. Add a building

## 3. Setup topologie 5

### 🌞  __Vous devez me rendre le show running-config de tous les équipements__

.route par défault déjà ajouté (voir plus haut)

