# Administration Linux et gestion des accès

## Objectif

Ce projet présente mes travaux pratiques en administration Linux et en sécurisation des accès utilisateurs sur un serveur Linux.

Les manipulations sont réalisées dans un environnement de laboratoire virtuel.

## Compétences travaillées

- Création et gestion des utilisateurs et groupes.
- Gestion des permissions Linux : `rwx`, `chmod`, `chown` et `chgrp`.
- Configuration des permissions par défaut avec `umask`.
- Gestion des ACL avec `getfacl` et `setfacl`.
- Gestion des services avec `systemctl`.
- Consultation et analyse des journaux système avec `journalctl`.
- Configuration de PAM pour renforcer l'authentification.
- Application d'une politique de mots de passe robustes.
- Mise en place d'une authentification multifacteur (MFA) avec Google Authenticator.
- Verrouillage et déverrouillage des comptes utilisateurs.

## 1. Utilisateurs et groupes

Commandes étudiées :

```bash
sudo useradd -m utilisateur1
sudo groupadd equipe
sudo usermod -aG equipe utilisateur1
id utilisateur1
groups utilisateur1
```

Ces commandes permettent de créer un utilisateur, de créer un groupe et d'ajouter l'utilisateur à ce groupe.

## 2. Permissions Linux

Les permissions `rwx` correspondent à :

- `r` : lecture.
- `w` : écriture.
- `x` : exécution ou traversée d'un répertoire.

Exemples :

```bash
chmod 750 fichier
sudo chown utilisateur1:equipe fichier
umask 027
```

## 3. Gestion des ACL

Les ACL permettent d'accorder des droits spécifiques à des utilisateurs ou groupes supplémentaires.

```bash
getfacl fichier
sudo setfacl -m u:utilisateur1:rw fichier
getfacl fichier
```

## 4. Services et journaux système

```bash
systemctl status ssh
sudo systemctl restart ssh
journalctl -u ssh
journalctl -p warning
```

Ces commandes permettent de vérifier l'état d'un service, de le redémarrer et de consulter les journaux système.

## 5. PAM et politique de mots de passe

PAM (Pluggable Authentication Modules) permet de contrôler les mécanismes d'authentification Linux.

Les travaux pratiques portent notamment sur :

- La longueur minimale des mots de passe.
- Les exigences de complexité.
- La limitation des tentatives de connexion.
- L'authentification multifacteur (MFA).

Les fichiers PAM doivent être modifiés avec précaution afin d'éviter de bloquer les connexions légitimes.

## 6. Verrouillage des comptes

Commandes étudiées :

```bash
sudo passwd -l utilisateur1
sudo passwd -S utilisateur1
sudo passwd -u utilisateur1
```

Ces commandes permettent respectivement de verrouiller un compte par mot de passe, de consulter son état et de le déverrouiller.

## Environnement de travail

- Ubuntu Server
- Terminal Linux
- Utilisateurs et groupes locaux
- PAM et outils système Linux

## Conclusion

Ces travaux pratiques permettent de développer des compétences en administration système Linux, en gestion des identités et des permissions, ainsi qu'en renforcement de la sécurité des accès.
