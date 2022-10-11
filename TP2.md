*** I.Setup IP

🌞 Mettez en place une configuration réseau fonctionnelle entre les deux machines:

 /// netsh interface ip show config ///

///
Configuration pour l'interface « Ethernet »
    DHCP activé:                          Oui
    Adresse IP :                           169.254.130.255
    Préfixe de sous-réseau :               169.254.0.0/16 (masque 255.255.0.0)
    Métrique de l'interface :             25
    Serveurs DNS configurés via DHCP :     Aucun
    Enregistrer avec le suffixe :           Principale uniquement
    Serveurs WINS configurés via DHCP :     Aucun

Configuration pour l'interface « VirtualBox Host-Only Network »
    DHCP activé :                         Non
    Adresse IP :                           192.168.56.1
    Préfixe de sous-réseau :               192.168.56.0/24 (masque 255.255.255.0)
    Métrique de l'interface :             25
    Serveurs DNS configurés statiquement : Aucun
    Enregistrer avec le suffixe :           Principale uniquement
    Serveurs WINS configurés statiquement : Aucun

Configuration pour l'interface « Connexion au réseau local* 1 »
    DHCP activé:                          Oui
    Métrique de l'interface :             25
    Serveurs DNS configurés via DHCP :     Aucun
    Enregistrer avec le suffixe :           Principale uniquement
    Serveurs WINS configurés via DHCP :     Aucun

Configuration pour l'interface « Connexion au réseau local* 2 »
    DHCP activé:                          Oui
    Métrique de l'interface :             25
    Serveurs DNS configurés via DHCP :     Aucun
    Enregistrer avec le suffixe :           Principale uniquement
    Serveurs WINS configurés via DHCP :     Aucun

Configuration pour l'interface « Wi-Fi »
    DHCP activé:                          Oui
    Adresse IP :                           10.33.17.54
    Préfixe de sous-réseau :               10.33.16.0/22 (masque 255.255.252.0)
    Passerelle par défaut :               10.33.19.254
    Métrique de passerelle :              0
    Métrique de l'interface :             55
    Serveurs DNS configurés via DHCP :     8.8.8.8
                                           8.8.4.4
                                           1.1.1.1
    Enregistrer avec le suffixe :           Principale uniquement
    Serveurs WINS configurés via DHCP :     Aucun

Configuration pour l'interface « Loopback Pseudo-Interface 1 »
    DHCP activé :                         Non
    Adresse IP :                           127.0.0.1
    Préfixe de sous-réseau :               127.0.0.0/8 (masque 255.0.0.0)
    Métrique de l'interface :             75
    Serveurs DNS configurés statiquement : Aucun
    Enregistrer avec le suffixe :           Principale uniquement
    Serveurs WINS configurés statiquement : Aucun
///

///
PS C:\Windows\system32> netsh interface ip set address "Ethernet" dhcp
DHCP est déjà activé sur cette interface.
///

///
PS C:\Windows\system32> netsh interface ip set address name= "Ethernet" static 10.24.19.254 255.255.252.0      
///

///
PS C:\Windows\system32> ipconfig

Configuration IP de Windows


Carte Ethernet Ethernet :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::8da0:a4cd:3fde:82ff%10
   Adresse IPv4. . . . . . . . . . . . . .: 10.24.19.254
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Passerelle par défaut. . . . . . . . . :

Carte Ethernet VirtualBox Host-Only Network :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::24d2:9fbb:b0a6:1e91%7
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.56.1
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Passerelle par défaut. . . . . . . . . :

Carte réseau sans fil Connexion au réseau local* 1 :

   Statut du média. . . . . . . . . . . . : Média déconnecté
   Suffixe DNS propre à la connexion. . . :

Carte réseau sans fil Connexion au réseau local* 2 :

   Statut du média. . . . . . . . . . . . : Média déconnecté
   Suffixe DNS propre à la connexion. . . :

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::2c24:4fc0:5566:cd8a%15
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.17.54
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Passerelle par défaut. . . . . . . . . : 10.33.19.254
///

L'IP à bien été modifié (youpi)
mon ip choisi : 10.24.19.254 (masque: 255.255.255.0)
l'ip choisi de Mathieu: 10.24.16.3 (masque: 255.255.252.0)
(sipcalc)
addresse de réseau: 10.24.16.0
l'adresse de broadcast: 10.24.19.255

** Prouvez que la connexion est fonctionnelle entre les deux machines:

///
PS C:\Windows\system32> ping 10.24.16.3

Envoi d’une requête 'Ping'  10.24.16.3 avec 32 octets de données :
Réponse de 10.24.16.3 : octets=32 temps=1 ms TTL=128
Réponse de 10.24.16.3 : octets=32 temps=1 ms TTL=128
Réponse de 10.24.16.3 : octets=32 temps=1 ms TTL=128
Réponse de 10.24.16.3 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 10.24.16.3:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 1ms, Moyenne = 1ms
///

🌞 Wireshark it:

voir packets ICMP

*** II. ARP my bro

🌞 Check the ARP table:

///
PS C:\Windows\system32> arp -a

Interface : 192.168.56.1 --- 0x7
  Adresse Internet      Adresse physique      Type
  192.168.56.255        ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.24.19.254 --- 0xa
  Adresse Internet      Adresse physique      Type
  *10.24.16.3            88-a4-c2-9c-99-84     dynamique*
  10.24.19.255          ff-ff-ff-ff-ff-ff     statique
  10.33.16.3            88-a4-c2-9c-99-84     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique

Interface : 10.33.17.54 --- 0xf
  Adresse Internet      Adresse physique      Type
  10.33.16.31           48-45-20-d6-79-6e     dynamique
  10.33.17.163          f0-6e-0b-d5-8f-f0     dynamique
  10.33.17.197          74-e5-f9-17-17-2c     dynamique
  10.33.18.103          48-a4-72-b7-37-7f     dynamique
  10.33.18.221          78-4f-43-87-f5-11     dynamique
  10.33.19.254          00-c0-e7-e0-04-4e     dynamique
  10.33.19.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
///

mon MAC : 00-26-b9-11-ed-dc
l'addresse MAC de Mathieu 10.33.16.3 est 88-a4-c2-9c-99-84

🌞déterminez la MAC de la gateway de votre réseau (celle du réseau Wifi YNOV):

Interface : 10.33.17.54 --- 0xf
  Adresse Internet      Adresse physique      Type
  10.33.16.31           48-45-20-d6-79-6e     dynamique
  10.33.17.163          f0-6e-0b-d5-8f-f0     dynamique
  10.33.17.197          74-e5-f9-17-17-2c     dynamique
  10.33.18.103          48-a4-72-b7-37-7f     dynamique
  10.33.18.221          78-4f-43-87-f5-11     dynamique
  *10.33.19.254          00-c0-e7-e0-04-4e     dynamique*
  10.33.19.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique


🌞 Manipuler la table ARP:

///
PS C:\Windows\system32> netsh interface IP delete arpcache
Ok.
///

///
PS C:\Windows\system32> arp -a

Interface : 192.168.56.1 --- 0x7
  Adresse Internet      Adresse physique      Type
  192.168.56.255        ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.24.19.254 --- 0xa
  Adresse Internet      Adresse physique      Type
  10.24.16.3            88-a4-c2-9c-99-84     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.33.17.54 --- 0xf
  Adresse Internet      Adresse physique      Type
  10.33.19.254          00-c0-e7-e0-04-4e     dynamique
  10.33.19.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
///
🌞 Wireshark it
