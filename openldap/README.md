# OpenLDAP — Ubuntu Server

## 📌 Présentation

Ce projet consiste à mettre en place un serveur LDAP sous Linux avec :

* Ubuntu Server comme serveur LDAP
* OpenLDAP comme service LDAP
* phpLDAPadmin comme interface graphique
* Rocky Linux comme client
* lab.local comme nom de domaine
* 192.168.1.100 comme adresse IP fixe du serveur

## 🏗️ Architecture

Rocky Linux
    │
    │ Firefox
    ▼
lab.local
    │
    ▼
192.168.1.100
    │
    ├── phpLDAPadmin
    │
    ▼
OpenLDAP
    │
    ▼
Base LDAP
dc=lab,dc=local

---

# 1. Configuration du serveur

Le serveur Ubuntu utilise l'adresse IP fixe 192.168.1.100.

Le nom de domaine utilisé est lab.local.

Le domaine LDAP utilisé est dc=lab,dc=local.

---

# 2. Installation d'OpenLDAP

On commence par mettre à jour les paquets :

sudo apt update

On installe ensuite OpenLDAP et les outils LDAP :

sudo apt install slapd ldap-utils -y

slapd permet de faire fonctionner le serveur OpenLDAP.

ldap-utils fournit les commandes nécessaires pour tester et utiliser LDAP.

---

# 3. Configuration d'OpenLDAP

On lance la configuration avec :

sudo dpkg-reconfigure slapd

On utilise lab.local comme domaine.

Le domaine LDAP devient dc=lab,dc=local.

Un mot de passe administrateur LDAP est également configuré.

---

# 4. Vérification du service LDAP

On vérifie que le service OpenLDAP fonctionne correctement avec :

sudo systemctl status slapd

Si le service est actif, le serveur OpenLDAP fonctionne correctement.

---

# 5. Test du serveur LDAP

On peut afficher les informations de la base LDAP avec :

sudo slapcat

On peut également tester une recherche LDAP avec :

ldapsearch -x -LLL -H ldap://localhost -b dc=lab,dc=local

Cette commande permet de vérifier que la base LDAP répond correctement.

---

# 6. Installation de l'interface graphique

Pour faciliter la gestion d'OpenLDAP, phpLDAPadmin est utilisé comme interface graphique.

Cette interface permet notamment de gérer les utilisateurs, les groupes et les différentes entrées de l'annuaire LDAP.

---

# 7. Accès depuis Rocky Linux

Rocky Linux est utilisé comme machine cliente.

Depuis Firefox, on entre :

http://lab.local

Le nom lab.local pointe vers l'adresse IP du serveur Ubuntu : 192.168.1.100.

L'interface graphique de gestion d'OpenLDAP peut alors être utilisée depuis Rocky Linux.

---

# 8. Résultat

Le serveur LDAP est configuré avec :

IP du serveur : 192.168.1.100
Nom de domaine : lab.local
Domaine LDAP : dc=lab,dc=local
Service : OpenLDAP
Interface graphique : phpLDAPadmin
Client : Rocky Linux

Ce projet permet de mettre en place un annuaire LDAP sous Linux et de le gérer depuis une interface graphique accessible depuis une machine cliente.
EOF.
