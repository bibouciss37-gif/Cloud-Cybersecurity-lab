# Sécurisation SSH sous Linux

## Objectif

Documenter l'installation, la configuration et la sécurisation du service SSH pour administrer un serveur Linux à distance.

## Compétences travaillées

- Installation et gestion du serveur OpenSSH.
- Connexion distante entre un client et un serveur.
- Authentification par mot de passe et par clé SSH.
- Durcissement de la configuration SSH.
- Gestion du pare-feu et du port SSH.
- Transfert sécurisé de fichiers avec SCP.
- Consultation des journaux et dépannage des connexions.

## 1. Vérifier le service SSH

```bash
sudo systemctl status ssh
```

## 2. Installer OpenSSH Server sur Ubuntu

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh
```

## 3. Vérifier l'adresse IP du serveur

```bash
ip addr
```

## 4. Se connecter depuis le client

```bash
ssh utilisateur@ADRESSE_IP_DU_SERVEUR
```

Remplacer `utilisateur` et `ADRESSE_IP_DU_SERVEUR` par les valeurs réelles du laboratoire.

## 5. Générer une clé SSH

Sur le client :

```bash
ssh-keygen -t ed25519
```

Copier la clé publique sur le serveur :

```bash
ssh-copy-id utilisateur@ADRESSE_IP_DU_SERVEUR
```

Tester ensuite la connexion par clé.

## 6. Transférer un fichier

```bash
scp fichier.txt utilisateur@ADRESSE_IP_DU_SERVEUR:/home/utilisateur/
```

## 7. Consulter les journaux

```bash
sudo journalctl -u ssh
```

## 8. Sécurisation

Les mesures étudiées comprennent :

- Limitation des comptes autorisés à se connecter.
- Utilisation de clés SSH.
- Désactivation éventuelle de l'authentification par mot de passe après vérification du fonctionnement des clés.
- Restriction des accès par pare-feu.
- Vérification régulière des journaux.

Toute modification de la configuration SSH doit être testée avant de fermer la session d'administration existante.

## Environnement

- Ubuntu Server
- Client Linux
- OpenSSH
- Réseau de laboratoire virtuel

## Conclusion

Ce projet présente les bases de l'administration distante et les principales mesures de sécurisation du service SSH.
