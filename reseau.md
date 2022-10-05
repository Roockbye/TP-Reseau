### TP:
## Exploration locale en solo
# 1. Affichage d'informations sur la pile TCP/IP locale
$ ipconfig /all
mac:  Adresse physique, 48-45-20-E8-FE-22
ip: 10.33.19.254
mac:  Adresse physique, 48-45-20-E8-FE-26
ip: média deconnecté

* Affichez votre gateway
ip: 10.33.19.254
$ arp -a 10.33.19.254
Mac: 00-c0-e7-e0-04-4e

* Trouvez comment afficher les informations sur une carte IP 
msconfig32 > Composants > Réseau > Carte

Nom	[00000002] Intel(R) Dual Band Wireless-AC 7265
Adresse IP	10.33.16.104, fe80::8829:eb42:945a:7d14
Adresse MAC 48:45:20:E8:FE:22
Passerelle IP par défaut	10.33.19.254

# 2. Modifications des informations

A. Modification d'adresse IP (part 1)
panneau config > Connections reseau et internet > Centre Reseau et partage > Modifier les paramètres de la carte > wifi (propriétés) > protocole internet version 4 (TCP/Ipv4) > Propriétés
* je n'ai pas perdue l'accès à internet

## Exploration locale en duo
🌞 Modifiez l'IP des deux machines pour qu'elles soient dans le même résea:
10.10.10.01
🌞 Vérifier à l'aide d'une commande que votre IP a bien été changée:
$ipconfig
Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.1
🌞 Vérifier que les deux machines se joignent:

Envoi d’une requête 'Ping'  10.10.10.2 avec 32 octets de données :
Réponse de 10.10.10.2 : octets=32 temps=2 ms TTL=64
Réponse de 10.10.10.2 : octets=32 temps=1 ms TTL=64
Réponse de 10.10.10.2 : octets=32 temps=1 ms TTL=64
Réponse de 10.10.10.2 : octets=32 temps<1ms TTL=64
🌞 Déterminer l'adresse MAC de votre correspondant:
arp -a 10.10.10.2
mac :88-a4-c2-ac-a8-2b
je n'ai pas pu faire la suite car port RJ45 cassé (suite partie à 2, compte-rendue de Killian et Mathieu)

# 4. Utilisation d'un des deux comme gateway

🌞Tester l'accès internet


## III. Manipulations d'autres outils/protocoles côté client
# 1. DHCP
ipconfig / all
Carte réseau sans fil Wi-Fi :

Suffixe DNS propre à la connexion. . . :
Description. . . . . . . . . . . . . . : Intel(R) Dual Band Wireless-AC 7265
Adresse physique . . . . . . . . . . . : 48-45-20-E8-FE-22
DHCP activé. . . . . . . . . . . . . . : Oui
Configuration automatique activée. . . : Oui
Adresse IPv6 de liaison locale. . . . .: fe80::8829:eb42:945a:7d14%4(préféré)
**Adresse IPv4. . . . . . . . . . . . . .: 10.33.16.104(préféré)**
Masque de sous-réseau. . . . . . . . . : 255.255.252.0
**Bail obtenu. . . . . . . . . . . . . . : mercredi 5 octobre 2022 08:48:19**
**Bail expirant. . . . . . . . . . . . . : jeudi 6 octobre 2022 08:45:36**
Passerelle par défaut. . . . . . . . . : 10.33.19.254
Serveur DHCP . . . . . . . . . . . . . : 10.33.19.254
IAID DHCPv6 . . . . . . . . . . . : 55067936
DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2A-8A-91-9F-A0-8C-FD-30-3E-F4
Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       8.8.4.4
                                       1.1.1.1
NetBIOS sur Tcpip. . . . . . . . . . . : Activé

# DNS
🌞** Trouver l'adresse IP du serveur DNS que connaît votre ordinateur**
ipconfig /all
**Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8**
🌞 Utiliser, en ligne de commande l'outil nslookup (Windows, MacOS) pour faire des requêtes DNS à la main
PS C:\Users\Melanie> nslookup google.com
Serveur :   dns.google
Address:  8.8.8.8   ---> serveur DNS

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:819::200e  --> l'IPv6 address du serveur
          142.250.178.142    -- > IPv4 address du serveur

PS C:\Users\Melanie> nslookup ynov.com
Serveur :   dns.google
Address:  8.8.8.8  ---> serveur DNS

Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:be9
          2606:4700:20::ac43:4ae2
          2606:4700:20::681a:ae9
          104.26.11.233
          104.26.10.233
          172.67.74.226
---> plusieurs address IPv4 et IPv6 car un site web peut faire appel à des services hébergés sur d'autres serveurs(comme une base de données) ou voir son contenu réparti sur différents serveurs web pour des raisons de performance

PS C:\Users\Melanie> nslookup 231.34.113.12
Serveur :   dns.google
Address:  8.8.8.8

dns.google ne parvient pas à trouver 231.34.113.12 : Non-existent domain  --> serveur non accessible
PS C:\Users\Melanie> nslookup 78.34.2.17
Serveur :   dns.google
Address:  8.8.8.8  ---> serveur DNS

Nom :    cable-78-34-2-17.nc.de
Address:  78.34.2.17  --> address IPv4 du serveur

## IV.Wireshark
![](https://i.imgur.com/key4FC4.png)