# Projet : Routage statique et dynamique OSPF dans une infrastructure multi-routeurs

## 1. Contexte professionnel

Après avoir segmenté le réseau local d'une entreprise avec des VLAN et mis en place le routage inter-VLAN, l'entreprise souhaite maintenant interconnecter plusieurs bâtiments ou sites distants.

L'objectif est de permettre aux différents réseaux de communiquer à travers plusieurs routeurs Cisco.

Pour répondre à ce besoin, deux méthodes de routage seront étudiées :

- Le routage statique.
- Le routage dynamique avec le protocole OSPF.

Le laboratoire permettra de comparer ces deux méthodes et de comprendre leur utilisation dans une infrastructure réseau professionnelle.

## 2. Objectifs du laboratoire

- Interconnecter plusieurs routeurs Cisco.
- Configurer les adresses IP des interfaces.
- Mettre en place des routes statiques.
- Configurer des routes statiques par défaut.
- Comprendre le fonctionnement du routage dynamique.
- Configurer le protocole OSPF.
- Définir les Router ID.
- Configurer les annonces réseau et les zones OSPF.
- Vérifier les voisins OSPF.
- Analyser les tables de routage.
- Diagnostiquer les problèmes de connectivité.

## 3. Architecture réseau

L'infrastructure comprend :

- 3 routeurs Cisco (R1, R2 et R3).
- 1 commutateur Cisco SW1 au siège.
- Plusieurs machines clientes.
- 3 VLAN au siège principal.
- 2 réseaux locaux distants.
- 2 liaisons inter-routeurs.

### Organisation des équipements

| Équipement | Fonction |
|---|---|
| R1 | Routeur du siège principal |
| R2 | Routeur du premier site distant |
| R3 | Routeur du deuxième site distant |
| SW1 | Commutateur du siège |
| PC1, PC2, PC3 | Clients des VLAN du siège |
| PC4 | Client du site distant 1 |
| PC5 | Client du site distant 2 |

## 4. Plan d'adressage IP

### Réseaux locaux

| Réseau | Fonction | Adresse réseau | Passerelle |
|---|---|---|---|
| VLAN 10 | Administration | 192.168.10.0/24 | 192.168.10.1 |
| VLAN 20 | Informatique | 192.168.20.0/24 | 192.168.20.1 |
| VLAN 30 | Comptabilité | 192.168.30.0/24 | 192.168.30.1 |
| LAN R2 | Site distant 1 | 192.168.40.0/24 | 192.168.40.1 |
| LAN R3 | Site distant 2 | 192.168.50.0/24 | 192.168.50.1 |

### Liaisons entre routeurs

| Liaison | Adresse réseau | Adresse IP |
|---|---|---|
| R1 - R2 | 10.0.12.0/30 | R1 : 10.0.12.1 / R2 : 10.0.12.2 |
| R2 - R3 | 10.0.23.0/30 | R2 : 10.0.23.1 / R3 : 10.0.23.2 |

## 5. Configuration des interfaces des routeurs

Les interfaces des routeurs doivent être configurées avec les adresses IP correspondant au plan d'adressage.

### Configuration de R1

```cisco
enable
configure terminal

interface gigabitEthernet 0/1
ip address 10.0.12.1 255.255.255.252
no shutdown
exit
```

Les sous-interfaces de R1 pour les VLAN 10, 20 et 30 doivent également être conservées conformément au laboratoire précédent.

### Configuration de R2

```cisco
enable
configure terminal

interface gigabitEthernet 0/0
ip address 10.0.12.2 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/1
ip address 10.0.23.1 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/2
ip address 192.168.40.1 255.255.255.0
no shutdown
exit
```

### Configuration de R3

```cisco
enable
configure terminal

interface gigabitEthernet 0/0
ip address 10.0.23.2 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/1
ip address 192.168.50.1 255.255.255.0
no shutdown
exit
```

Remarque : les noms des interfaces peuvent varier selon les modèles de routeurs utilisés dans Packet Tracer.

## 6. Première partie : configuration du routage statique

Le routage statique consiste à configurer manuellement les routes permettant aux routeurs d'atteindre les réseaux distants.

### Sur R1

```cisco
ip route 192.168.40.0 255.255.255.0 10.0.12.2
ip route 192.168.50.0 255.255.255.0 10.0.12.2
```

### Sur R2

```cisco
ip route 192.168.10.0 255.255.255.0 10.0.12.1
ip route 192.168.20.0 255.255.255.0 10.0.12.1
ip route 192.168.30.0 255.255.255.0 10.0.12.1

ip route 192.168.50.0 255.255.255.0 10.0.23.2
```

### Sur R3

```cisco
ip route 192.168.10.0 255.255.255.0 10.0.23.1
ip route 192.168.20.0 255.255.255.0 10.0.23.1
ip route 192.168.30.0 255.255.255.0 10.0.23.1
ip route 192.168.40.0 255.255.255.0 10.0.23.1
```

Les réseaux directement connectés n'ont pas besoin de routes statiques supplémentaires.

## 7. Deuxième partie : configuration des routes statiques par défaut

Une route par défaut permet à un routeur de transmettre les paquets vers un routeur suivant lorsque la destination ne figure pas dans sa table de routage.

### Sur R3

```cisco
ip route 0.0.0.0 0.0.0.0 10.0.23.1
```

### Sur R2

```cisco
ip route 0.0.0.0 0.0.0.0 10.0.12.1
```

Ces routes remplacent les routes spécifiques vers les réseaux situés en amont pour cette partie du laboratoire.

Il faut également prévoir les routes de retour nécessaires pour assurer une communication dans les deux sens.

## 8. Troisième partie : configuration du routage dynamique OSPF

OSPF (Open Shortest Path First) est un protocole de routage dynamique permettant aux routeurs d'échanger automatiquement des informations sur les réseaux accessibles.

### Configuration sur R1

```cisco
router ospf 1
router-id 1.1.1.1

network 192.168.10.0 0.0.0.255 area 0
network 192.168.20.0 0.0.0.255 area 0
network 192.168.30.0 0.0.0.255 area 0
network 10.0.12.0 0.0.0.3 area 0
```

### Configuration sur R2

```cisco
router ospf 1
router-id 2.2.2.2

network 10.0.12.0 0.0.0.3 area 0
network 10.0.23.0 0.0.0.3 area 0
network 192.168.40.0 0.0.0.255 area 0
```

### Configuration sur R3

```cisco
router ospf 1
router-id 3.3.3.3

network 10.0.23.0 0.0.0.3 area 0
network 192.168.50.0 0.0.0.255 area 0
```

Les trois routeurs utilisent ici la zone backbone area 0.

Avant de tester OSPF, supprimer les routes statiques de la première partie afin de comparer correctement les deux méthodes.

## 9. Vérification des configurations OSPF

Commandes Cisco :

```cisco
show ip ospf neighbor
show ip protocols
show ip ospf interface brief
show ip route ospf
show ip route
```

Résultats attendus :

- Les voisins OSPF sont correctement établis.
- Les Router ID sont correctement définis.
- Les réseaux distants sont annoncés.
- Les routes OSPF apparaissent dans les tables de routage avec le code O.
- Les routeurs apprennent automatiquement les réseaux distants.

## 10. Tests de connectivité

Depuis PC1 :

```bash
ping 192.168.40.10
ping 192.168.50.10
```

Depuis PC4 :

```bash
ping 192.168.10.10
ping 192.168.50.10
```

Depuis PC5 :

```bash
ping 192.168.20.10
ping 192.168.40.10
```

Résultats attendus :

- Communication entre les différents sites.
- Communication entre les VLAN du siège et les réseaux distants.
- Communication dans les deux sens.
- Accessibilité des réseaux après configuration des routes.

## 11. Vérification et diagnostic

Commandes utilisées :

```cisco
show ip interface brief
show ip route
show ip route static
show ip route ospf
show ip ospf neighbor
show ip protocols
show running-config
```

Ces commandes permettent de vérifier :

- L'état des interfaces.
- Les routes statiques installées.
- Les routes apprises par OSPF.
- Les relations de voisinage entre routeurs.
- Les paramètres du protocole de routage.

## 12. Sécurisation et améliorations possibles

Les améliorations envisagées comprennent :

- Configuration de plusieurs zones OSPF.
- Annonce d'une route par défaut avec OSPF.
- Mise en place d'ACL pour contrôler les communications entre les sites.
- Modification des coûts OSPF.
- Étude de la convergence OSPF.
- Mise en place d'une authentification OSPF.
- Ajout d'un quatrième routeur pour étendre l'infrastructure.

## 13. Environnement technique

| Élément | Technologie |
|---|---|
| Simulation réseau | Cisco Packet Tracer |
| Équipements | Routeurs et commutateurs Cisco |
| Routage statique | Cisco IOS |
| Routage dynamique | OSPFv2 |
| Zones OSPF | Area 0 |
| Tests | Ping et commandes Cisco |

## 14. Compétences développées

- Interconnexion de plusieurs réseaux.
- Configuration et administration des routeurs Cisco.
- Configuration des routes statiques.
- Configuration des routes par défaut.
- Compréhension du routage dynamique.
- Configuration des Router ID.
- Configuration des zones OSPF.
- Analyse des tables de routage.
- Vérification des voisins OSPF.
- Diagnostic des problèmes de connectivité.

## 15. Résultat du laboratoire

Ce laboratoire permet de faire évoluer une infrastructure réseau locale segmentée en une infrastructure multi-sites.

Il permet également de comparer le routage statique et le routage dynamique OSPF dans un contexte professionnel.

Les configurations, captures Packet Tracer et résultats des tests seront ajoutés après réalisation et validation du laboratoire.
