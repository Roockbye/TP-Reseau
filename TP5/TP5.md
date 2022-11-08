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