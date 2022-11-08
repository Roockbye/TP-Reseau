# TP4 : TCP, UDP et services réseau

## I. First steps
Top 5 des applications:
(discord, Spotify, github, Microsoft Teams, App météo )


🌞 __Demandez l'avis à votre OS:__

commande pour Windows:

```netstat -nb```

🌞 __Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP:__

🦈 [Teams](./teams_netstat.pcapng) : --> TCP
```
  TCP    10.188.166.93:49455    52.112.120.15:443      ESTABLISHED
 [Teams.exe]
 ```

IP: 52.112.120.15

Port: 443

port local: 49455

🦈 [Discord](./discord_netstat.pcapng) : --> TCP
```
  TCP    10.188.166.93:59736   162.159.134.234:443    ESTABLISHED
 [Discord.exe]
 ```

IP: 162.159.134.234

Port: 443

Port local: 59736

🦈 [Spotify](./spotify_netstat.pcapng) : --> TCP

IP: 162.159.134.234

Port: 443

Port local: 59736

🦈 [Firefox](./firefox_netstat.pcapng) : --> TCP

IP: 152.199.19.161

Port: 80

Port local: 53612

🦈 [Weatherapp](./weather_netstat.pcapng) : --> TCP

IP: 184.85.187.205

Port: 443

Port local: 53976

## II. Mise en place

### 1. SSH


🌞 __Examinez le trafic dans Wireshark:__

La connexion SSH est en TCP et pas UDP car TCP est lent mais fiable et UDP rapide mais non fiable. Et pour une connexion SSH on préfère avoir une connection fiable malgré qu'elle soit lente.



🌞 __Demandez aux OS:__

```netstat -nb```  sur windows

```
  TCP    10.4.1.1:52038         10.4.1.11:22           ESTABLISHED
 [ssh.exe]
```
Sur VM:

```
[melanie@node1 ~]$ sudo ss -tn
[sudo] password for melanie:
State   Recv-Q   Send-Q     Local Address:Port     Peer Address:Port   Process
ESTAB   0        36             10.4.1.11:22           10.4.1.1:52038
```
🦈[3way_handshakes](./3way_handshakes.pcapng)

(la MAC est différente car j'ai voulu refaire une capture whireshark mieux sur mon nouvel ordi)
# III. DNS

### 2. Setup
🌞 __Un cat des fichiers de conf:__

__fichier de configuration principal:__

```
[melanie@dns var]$ sudo cat /etc/named.conf
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };
        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

        dnssec-enable yes;
        dnssec-validation yes;

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

# référence vers notre fichier de zone
zone "tp4.b1" IN {
     type master;
     file "tp4.b1.db";
     allow-update { none; };
     allow-query {any; };
};
# référence vers notre fichier de zone inverse
zone "1.4.10.in-addr.arpa" IN {
     type master;
     file "tp4.b1.rev";
     allow-update { none; };
     allow-query { any; };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

__fichier de zone:__

```
[melanie@dns var]$ sudo cat /var/named/tp4.b1.db
$TTL 86400
@ IN SOA dns-server.tp4.b1. admin.tp4.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns-server.tp4.b1.

; Enregistrements DNS pour faire correspondre des noms à des IPs
dns-server IN A 10.4.1.201
node1      IN A 10.4.1.11
```

__fichier de zone inverse:__

```
[melanie@dns var]$ sudo cat /var/named/tp4.b1.rev
$TTL 86400
@ IN SOA dns-server.tp4.b1. admin.tp4.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns-server.tp4.b1.

;Reverse lookup for Name Server
201 IN PTR dns-server.tp4.b1.
11 IN PTR node1.tp4.b1.
```

🌞 __Un systemctl status named qui prouve que le service tourne bien:__
```
[melanie@dns var]$ sudo systemctl start named
[melanie@dns var]$ sudo systemctl enablenamed
Unknown operation enablenamed.
[melanie@dns var]$ sudo systemctl enable named
Created symlink /etc/systemd/system/multi-user.target.wants/named.service → /usr/lib/systemd/system/named.service.
[melanie@dns var]$ systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
   Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2022-10-28 17:13:22 CEST; 21s ago
 Main PID: 5418 (named)
    Tasks: 5 (limit: 2716)
   Memory: 27.4M
   CGroup: /system.slice/named.service
           └─5418 /usr/sbin/named -u named -c /etc/named.conf

Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './NS/IN': 2001:503:c27::2:30#53
Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './DNSKEY/IN': 2001:dc3::35#53
Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './NS/IN': 2001:dc3::35#53
Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './DNSKEY/IN': 2001:7fd::1#53
Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './NS/IN': 2001:7fd::1#53
Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './DNSKEY/IN': 2001:7fd::1#53
Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './DNSKEY/IN': 2001:500:a8::e#53
Oct 28 17:13:22 dns.localdomain named[5418]: network unreachable resolving './DNSKEY/IN': 2001:503:ba3e::2:30#53
Oct 28 17:13:22 dns.localdomain named[5418]: managed-keys-zone: Key 20326 for zone . acceptance timer complete: key now trusted
Oct 28 17:13:22 dns.localdomain named[5418]: resolver priming query complete
```
🌞 __une commande ss qui prouve que le service écoute bien sur un port:__

```
[melanie@dns var]$ ss -tpunl
Netid  State   Recv-Q  Send-Q   Local Address:Port   Peer Address:Port Process
udp    UNCONN  0       0           10.4.1.201:53          0.0.0.0:*
udp    UNCONN  0       0            127.0.0.1:53          0.0.0.0:*
udp    UNCONN  0       0            127.0.0.1:323         0.0.0.0:*
udp    UNCONN  0       0                [::1]:53             [::]:*
udp    UNCONN  0       0                [::1]:323            [::]:*
tcp    LISTEN  0       10          10.4.1.201:53          0.0.0.0:*
tcp    LISTEN  0       10           127.0.0.1:53          0.0.0.0:*
tcp    LISTEN  0       128            0.0.0.0:22          0.0.0.0:*
tcp    LISTEN  0       128          127.0.0.1:953         0.0.0.0:*
tcp    LISTEN  0       10               [::1]:53             [::]:*
tcp    LISTEN  0       128               [::]:22             [::]:*
tcp    LISTEN  0       128              [::1]:953            [::]:*
```

🌞 __Ouvrez le bon port dans le firewall:__

```
tcp    LISTEN  0       10          10.4.1.201:53          0.0.0.0:*
```

port en écoute: 53


__.__ Ouvrez ce port dans le firewall de la machine dns-server.tp4.b1:
```
[melanie@dns var]$ sudo firewall-cmd --list-all
[sudo] password for melanie:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s9
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
[melanie@dns var]$ sudo firewall-cmd --add-port=53/udp --permanent
success
[melanie@dns var]$ sudo firewall-cmd --reload
success
```

### 3. Test

🌞 __Sur la machine node1.tp4.b1:__

configurez la machine pour qu'elle utilise votre serveur DNS quand elle a besoin de résoudre des noms:

```
[melanie@node1 ~]$ sudo cat /etc/sysconfig/network-scripts/ifcfg-enp0s9
DEVICE=enp0s9

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.4.1.11
NETMASK=255.255.255.0

GATEWAY=10.4.1.254

DNS1=10.4.1.201
```

dig dns-server.tp4.b1

```
[melanie@node1 ~]$ dig dns-server.tp4.b1

; <<>> DiG 9.11.36-RedHat-9.11.36-3.el8_6.1 <<>> dns-server.tp4.b1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 55981
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;dns-server.tp4.b1.             IN      A

;; AUTHORITY SECTION:
.                       86344   IN      SOA     a.root-servers.net. nstld.verisign-grs.com. 2022102800 1800 900 604800 86400

;; Query time: 26 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Fri Oct 28 17:44:32 CEST 2022
;; MSG SIZE  rcvd: 121
```

dig node1.tp4.b1
```
[melanie@node1 ~]$ dig node1.tp4.b1

; <<>> DiG 9.11.36-RedHat-9.11.36-3.el8_6.1 <<>> node1.tp4.b1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 11627
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;node1.tp4.b1.                  IN      A

;; AUTHORITY SECTION:
.                       86292   IN      SOA     a.root-servers.net. nstld.verisign-grs.com. 2022102800 1800 900 604800 86400

;; Query time: 26 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Fri Oct 28 17:45:20 CEST 2022
;; MSG SIZE  rcvd: 116
```

dig google.com

```
[melanie@node1 ~]$ dig google.com

; <<>> DiG 9.11.36-RedHat-9.11.36-3.el8_6.1 <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 62790
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             139     IN      A       142.250.74.238

;; Query time: 22 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Fri Oct 28 17:45:09 CEST 2022
;; MSG SIZE  rcvd: 55
```

🌞 __Sur votre PC:__

__.__ Utilisez une commande pour résoudre le nom node1.tp4.b1 en utilisant 10.4.1.201 comme serveur DNS:

```
[melanie@node1 ~]$ nslookup node1.tp4.b1 10.4.1.201
Server:         10.4.1.201
Address:        10.4.1.201#53

Name:   node1.tp4.b1
Address: 10.4.1.11
```
🦈 

(Je n'ai pas récupérer mes anciennes VMs, ni cette capture de mon ancien ordi)