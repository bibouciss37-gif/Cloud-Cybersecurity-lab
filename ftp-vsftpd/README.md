# Lab FTP avec vsftpd et FileZilla

##  Contexte

Mise en place d'un serveur FTP sous **Ubuntu Server** avec le service **vsftpd**.

Une machine **Rocky Linux** a été utilisée comme client. **FileZilla** a été installé sur Rocky Linux afin de se connecter au serveur FTP et de tester le transfert de fichiers.

### Architecture

                 ┌─────────────────────────┐
                 │      Ubuntu Server      │
                 │                         │
                 │   IP : 192.168.1.100    │
                 │                         │
                 │         vsftpd          │
                 │       Serveur FTP       │
                 └────────────┬────────────┘
                              │
                              │ FTP
                              │ Port 21
                              ▼
                 ┌─────────────────────────┐
                 │      Rocky Linux       │
                 │                         │
                 │        FileZilla        │
                 │       Client FTP        │
                 └─────────────────────────┘

---

##  1. Installation de vsftpd

Sur Ubuntu Server, installation du serveur FTP :


sudo apt update
sudo apt install -y vsftpd


Vérification du service :

sudo systemctl status vsftpd


Le service `vsftpd` est utilisé pour fournir le service FTP sur le serveur Ubuntu.

---

## 2. Fichier de configuration

Le fichier principal de configuration de vsftpd est :

text:
/etc/vsftpd.conf

Pour le modifier :

sudo nano /etc/vsftpd.conf

Plusieurs paramètres permettent de contrôler le comportement et la sécurité du serveur FTP.

---

##  3. Configuration de l'accès utilisateur

L'accès anonyme a été désactivé :

```conf
anonymous_enable=NO


Cela signifie que les utilisateurs ne peuvent pas se connecter au serveur FTP de manière anonyme.

L'écriture a été activée :

```conf
write_enable=YES


Cette option permet d'autoriser les opérations d'écriture, comme l'envoi de fichiers, sous réserve des permissions de l'utilisateur et des autres paramètres de configuration.

---

##  4. Isolation des utilisateurs avec chroot

Pour limiter les déplacements de l'utilisateur FTP dans le système de fichiers :

```conf
chroot_local_user=YES


Cette configuration permet de placer l'utilisateur dans un environnement limité à son répertoire personnel.

L'objectif est d'éviter qu'un utilisateur FTP puisse parcourir librement les répertoires situés en dehors de son espace prévu.

---

##  5. Configuration du mode passif

Le mode passif permet au client FTP d'établir les connexions de données nécessaires au transfert des fichiers.

Exemple de configuration :

``conf
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=50000


La plage `40000-50000` est utilisée pour les connexions de données en mode passif.

> **Remarque :** si une autre plage de ports avait été utilisée dans le TP original, elle doit être remplacée ici par la valeur réellement configurée.

---

##  6. Configuration du pare-feu UFW

Le port de contrôle FTP doit être autorisé :


sudo ufw allow 21/tcp


Lorsque le mode passif est utilisé, la plage de ports configurée doit également être autorisée.

Avec l'exemple précédent :


sudo ufw allow 40000:50000/tcp

Vérification des règles :


sudo ufw status


Les ports utilisés sont donc :

| Port              | Utilisation                          |
| ----------------- | ------------------------------------ |
| `21/tcp`          | Connexion de contrôle FTP            |
| `40000-50000/tcp` | Connexions de données en mode passif |

---

##  7. Redémarrage du serveur FTP

Après modification du fichier `/etc/vsftpd.conf`, le service doit être redémarré :


sudo systemctl restart vsftpd


Puis vérifier son état :

sudo systemctl status vsftpd


---

##  8. Utilisation d'un compte utilisateur

La connexion FTP se fait avec un compte utilisateur Linux autorisé.

Les informations utilisées dans FileZilla sont :

text:

Hôte        : 192.168.1.100
Protocole   : FTP
Port        : 21
Utilisateur : nom_utilisateur
Mot de passe: mot_de_passe


Le compte utilisé doit disposer des permissions nécessaires pour accéder et éventuellement écrire dans son répertoire.

---

##  9. Connexion avec FileZilla

Sur **Rocky Linux**, FileZilla est utilisé comme client FTP.

Une fois les informations de connexion renseignées, FileZilla permet d'afficher les fichiers du serveur distant et ceux de la machine cliente.

La connexion suit le schéma :

text:
Rocky Linux / FileZilla
          │
          │ Port 21
          ▼
Ubuntu Server / vsftpd
          │
          │
          ▼
Répertoire de l'utilisateur
```

---

##  10. Transfert de fichiers

Une fois connecté, des fichiers peuvent être transférés entre Rocky Linux et Ubuntu Server.

Les opérations testées peuvent notamment être :

* téléchargement d'un fichier depuis le serveur ;
* envoi d'un fichier vers le serveur ;
* consultation du contenu du répertoire FTP ;
* vérification des permissions.

Ces tests permettent de vérifier que le serveur FTP fonctionne correctement.

---

##  11. Vérifications côté serveur

Vérifier que vsftpd fonctionne :

sudo systemctl status vsftpd

Vérifier que le serveur écoute sur le port FTP :

sudo ss -ltnp | grep :21

Vérifier les règles du pare-feu :

sudo ufw status

---

## 12. Problèmes possibles

Lorsqu'un serveur FTP utilise le mode passif, plusieurs éléments doivent être correctement configurés :

* le port `21/tcp` doit être accessible ;
* la plage de ports passifs doit être autorisée dans le pare-feu ;
* FileZilla doit utiliser le mode passif ;
* les permissions du compte utilisateur doivent être correctes ;
* le service `vsftpd` doit être actif.

Une mauvaise configuration de la plage passive peut notamment empêcher les transferts de fichiers alors que la connexion au serveur fonctionne.

---

##  Ce que j'ai appris

Grâce à ce lab, j'ai appris à :

* installer et gérer le service **vsftpd** ;
* utiliser le fichier `/etc/vsftpd.conf` ;
* désactiver les connexions anonymes avec `anonymous_enable=NO` ;
* autoriser les opérations d'écriture avec `write_enable=YES` ;
* isoler les utilisateurs avec `chroot_local_user=YES` ;
* comprendre le fonctionnement du **mode passif FTP** ;
* définir une plage de ports passifs ;
* configurer le pare-feu UFW pour le FTP ;
* utiliser **FileZilla** comme client FTP ;
* transférer des fichiers entre une machine cliente et un serveur Linux ;
* vérifier l'état d'un service avec `systemctl` ;
* vérifier les ports en écoute avec `ss`.

---

##  Prochaines étapes

* [ ] Approfondir les permissions Linux pour les utilisateurs FTP
* [ ] Configurer plusieurs utilisateurs FTP
* [ ] Créer des répertoires FTP dédiés
* [ ] Étudier les différences entre FTP, FTPS et SFTP
* [ ] Tester la sécurisation de FTP avec TLS/SSL
* [ ] Tester les connexions depuis plusieurs clients

---

##  Environnement

| Élément            | Technologie       |
| ------------------ | ----------------- |
| Serveur            | Ubuntu Server     |
| Service FTP        | vsftpd            |
| Adresse IP serveur | `192.168.1.100`   |
| Client             | Rocky Linux       |
| Client FTP         | FileZilla         |
| Protocole          | FTP               |
| Port de contrôle   | `21/tcp`          |
| Mode de transfert  | Passif            |
| Plage passive      | `40000-50000/tcp` |

