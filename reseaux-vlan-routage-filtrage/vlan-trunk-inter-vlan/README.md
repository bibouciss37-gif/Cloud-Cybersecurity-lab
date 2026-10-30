# Projet : Segmentation réseau d'une entreprise avec VLAN, trunk et routage inter-VLAN

## 1. Contexte professionnel

Une entreprise possède trois services : Administration, Informatique et Comptabilité.

Afin d'améliorer l'organisation et la sécurité de son infrastructure réseau, elle souhaite segmenter son réseau local en plusieurs VLAN.

L'objectif est de séparer les différents services tout en permettant leur communication grâce au routage inter-VLAN.

## 2. Objectifs du laboratoire

- Créer plusieurs VLAN pour segmenter le réseau.
- Affecter les ports des commutateurs aux VLAN correspondants.
- Configurer un lien trunk IEEE 802.1Q.
- Mettre en place le routage inter-VLAN avec la technique Router-on-a-Stick.
- Vérifier la communication entre les différents réseaux.
- Diagnostiquer les problèmes de connectivité.

## 3. Architecture réseau

L'infrastructure comprend :

- 1 routeur Cisco R1.
- 1 commutateur Cisco SW1.
- 3 machines clientes.
- 3 VLAN correspondant aux services de l'entreprise.

## 4. Plan d'adressage IP

| VLAN | Service | Réseau | Passerelle |
|---|---|---|---|
| 10 | Administration | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Informatique | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Comptabilité | 192.168.30.0/24 | 192.168.30.1 |

### Adressage des machines

| Machine | VLAN | Adresse IP | Passerelle |
|---|---|---|---|
| PC1 | 10 | 192.168.10.10 | 192.168.10.1 |
| PC2 | 20 | 192.168.20.10 | 192.168.20.1 |
| PC3 | 30 | 192.168.30.10 | 192.168.30.1 |

## 5. Configuration des VLAN sur le commutateur SW1

Création des VLAN :

```cisco
enable
configure terminal

vlan 10
name ADMINISTRATION
exit

vlan 20
name INFORMATIQUE
exit

vlan 30
name COMPTABILITE
exit
```

Affectation des ports :

```cisco
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit

interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit

interface fastEthernet 0/3
switchport mode access
switchport access vlan 30
exit
```

Vérification :

```cisco
show vlan brief
```

## 6. Configuration du lien trunk

Le lien trunk permet de transporter le trafic de plusieurs VLAN entre le commutateur et le routeur.

Sur SW1 :

```cisco
interface gigabitEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30
exit
```

Vérification :

```cisco
show interfaces trunk
```

## 7. Configuration du routage inter-VLAN

Le routage inter-VLAN permet aux machines appartenant à différents VLAN de communiquer entre elles.

La technique utilisée est Router-on-a-Stick.

Sur le routeur R1 :

```cisco
enable
configure terminal

interface gigabitEthernet 0/0
no shutdown
exit

interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit

interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit

interface gigabitEthernet 0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit
```

Vérification :

```cisco
show ip interface brief
show ip route
```

## 8. Configuration des machines clientes

Configurer les adresses IP suivantes dans Packet Tracer :

- PC1 : 192.168.10.10 /24
- PC2 : 192.168.20.10 /24
- PC3 : 192.168.30.10 /24

Configurer également les passerelles par défaut :

- PC1 : 192.168.10.1
- PC2 : 192.168.20.1
- PC3 : 192.168.30.1

## 9. Tests de connectivité

Depuis PC1 :

```bash
ping 192.168.10.1
ping 192.168.20.10
ping 192.168.30.10
```

Depuis PC2 :

```bash
ping 192.168.20.1
ping 192.168.10.10
```

Depuis PC3 :

```bash
ping 192.168.30.1
ping 192.168.10.10
```

Résultats attendus :

- Communication entre chaque PC et sa passerelle.
- Communication entre les machines appartenant à différents VLAN après configuration du routage inter-VLAN.
- Absence de communication directe au niveau 2 entre les VLAN.

## 10. Vérification et diagnostic

Commandes utilisées :

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show running-config
```

Ces commandes permettent de vérifier les VLAN, les liens trunk, les interfaces du routeur et les routes installées.

## 11. Sécurisation et améliorations possibles

Les améliorations envisagées comprennent :

- Mise en place d'ACL pour contrôler les communications entre les services.
- Limitation des VLAN autorisés sur les trunks.
- Désactivation des ports inutilisés.
- Mise en place de Port Security.
- Configuration de DHCP pour automatiser l'adressage IP.

## 12. Environnement technique

| Élément | Technologie |
|---|---|
| Simulation réseau | Cisco Packet Tracer |
| Routage | Cisco IOS |
| Commutation | Switch Cisco |
| Segmentation | VLAN |
| Encapsulation | IEEE 802.1Q |
| Routage inter-VLAN | Router-on-a-Stick |
| Tests | Ping et commandes Cisco |

## 13. Compétences développées

- Segmentation d'un réseau d'entreprise.
- Configuration et administration des VLAN.
- Configuration des liens trunk.
- Compréhension de l'encapsulation IEEE 802.1Q.
- Mise en place du routage inter-VLAN.
- Configuration des passerelles.
- Vérification et dépannage de la connectivité réseau.

## 14. Résultat du laboratoire

Ce laboratoire permet de mettre en pratique la segmentation réseau d'une entreprise, la configuration des commutateurs Cisco et le routage inter-VLAN.

Les résultats et captures d'écran seront ajoutés après réalisation et validation du laboratoire.
