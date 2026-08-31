# RAID 1 + LVM — Rocky Linux

## 📌 Présentation

Ce projet consiste à mettre en place une architecture de stockage combinant :

* **RAID 1** pour la redondance des données
* **LVM** pour la gestion flexible du stockage
* **XFS** comme système de fichiers
* **Rocky Linux** comme système d'exploitation

L'architecture utilisée est la suivante :

/dev/sdb ─────┐
              │
              ├── RAID 1 ── /dev/md0
              │
/dev/sdc ─────┘
                     │
                     ▼
                  PV LVM
                     │
                     ▼
                  VG LVM
                  vgdata
                     │
                     ▼
                  LV LVM
                  lvdata
                     │
                     ▼
                  XFS
                     │
                     ▼
                /mnt/raid1

---

# 1. Vérification des disques

Avant de commencer, on vérifie les disques disponibles :


lsblk

Exemple :

NAME   SIZE TYPE
sda    256G disk
├─sda1  1G part
└─sda2 255G part
sdb      4G disk
sdc      4G disk


Dans ce lab :

* `/dev/sda` → disque principal de Rocky Linux
* `/dev/sdb` → premier disque destiné au RAID
* `/dev/sdc` → deuxième disque destiné au RAID

⚠️ **Attention : `/dev/sdb` et `/dev/sdc` seront utilisés pour le RAID. Les données présentes sur ces disques peuvent être supprimées.**

---

# 2. Installer les outils nécessaires

On installe `mdadm` pour gérer le RAID et les outils LVM :


sudo dnf install mdadm lvm2 -y


Vérification :

mdadm --version

Et :

lvm version

---

# 3. Vérifier les disques avant la création du RAID

On peut vérifier que les disques ne contiennent pas déjà de configuration importante :

sudo fdisk -l

On peut également utiliser :

lsblk -f

---

# 4. Créer le RAID 1

Le RAID 1 utilise deux disques et conserve une copie identique des données sur chacun.

Création du RAID :

sudo mdadm --create --verbose /dev/md0 \
--level=1 \
--raid-devices=2 \
/dev/sdb /dev/sdc

### Explication

* `/dev/md0` → nom du périphérique RAID
* `--level=1` → RAID 1
* `--raid-devices=2` → deux disques
* `/dev/sdb` → premier disque
* `/dev/sdc` → deuxième disque

---

# 5. Vérifier l'état du RAID

Pour voir la synchronisation :

cat /proc/mdstat

Exemple :

Personalities : [raid1]
md0 : active raid1 sdc[1] sdb[0]
      4193280 blocks super 1.2 [2/2] [UU]

`[UU]` signifie que les deux disques sont actifs.

On peut également utiliser :

sudo mdadm --detail /dev/md0

Cette commande donne davantage d'informations sur le RAID.

---

# 6. Vérifier le RAID avec lsblk

lsblk

On devrait maintenant avoir quelque chose ressemblant à :

sdb      4G
└─md0    4G
sdc      4G
└─md0    4G

Les deux disques physiques sont donc regroupés dans `/dev/md0`.

---

# 7. Créer le Physical Volume (PV)

LVM va maintenant être installé **au-dessus du RAID**.

On transforme `/dev/md0` en Physical Volume :

sudo pvcreate /dev/md0

Vérification :

sudo pvs

Exemple :

PV         VG      Fmt  Attr PSize  PFree
/dev/md0          lvm2       4.00g  4.00g


---

# 8. Créer le Volume Group (VG)

On crée un groupe de volumes nommé `vgdata` :

sudo vgcreate vgdata /dev/md0

Vérification :

sudo vgs

Exemple :

VG      #PV #LV #SN Attr   VSize  VFree
vgdata    1   0   0 wz--n-  4.00g  4.00g

---

# 9. Créer le Logical Volume (LV)

On crée maintenant un volume logique de 3 Go :

sudo lvcreate -L 3G -n lvdata vgdata

### Explication

* `-L 3G` → taille du volume logique : 3 Go
* `-n lvdata` → nom du Logical Volume
* `vgdata` → Volume Group dans lequel le LV est créé

Vérification :

sudo lvs

On devrait obtenir :

LV      VG      Attr       LSize
lvdata  vgdata  -wi-a----- 3.00g

---

# 10. Vérifier toute la structure LVM

On peut utiliser :

sudo pvs

Puis :

sudo vgs

Puis :

sudo lvs

La structure est maintenant :

/dev/md0
   │
   ▼
PV
   │
   ▼
vgdata
   │
   ▼
lvdata

---

# 11. Créer le système de fichiers

On utilise XFS :

sudo mkfs.xfs /dev/vgdata/lvdata

Vérification :

lsblk -f

On devrait voir `xfs` associé à `lvdata`.

---

# 12. Créer le point de montage

On crée le répertoire qui servira à accéder au stockage :

sudo mkdir -p /mnt/raid1

---

# 13. Monter le volume

sudo mount /dev/vgdata/lvdata /mnt/raid1

Vérification :

df -h

On devrait avoir une ligne similaire à :

/dev/mapper/vgdata-lvdata    3.0G   ...   /mnt/raid1

---

# 14. Tester le stockage

On crée un fichier dans le volume :

sudo touch /mnt/raid1/test.txt

On vérifie :

ls -l /mnt/raid1

On peut également écrire du contenu :

echo "Test RAID 1 + LVM" | sudo tee /mnt/raid1/test.txt

Puis :

cat /mnt/raid1/test.txt

Résultat :

Test RAID 1 + LVM


---

# 15. Vérifier l'espace disponible

df -h /mnt/raid1

On peut également utiliser :

sudo lvs

et :

sudo vgs

---

# 16. Afficher l'ensemble de l'architecture

La commande :

lsblk

permet de visualiser la hiérarchie des périphériques.

On devrait obtenir une structure similaire à :

sdb
└─md0
  └─vgdata-lvdata
sdc
└─md0
  └─vgdata-lvdata

On peut également utiliser :

sudo blkid

pour afficher les UUID et les systèmes de fichiers.

---

# 17. Vérifier les informations du RAID

sudo mdadm --detail /dev/md0

Cette commande permet notamment de vérifier :

* le niveau RAID
* les disques utilisés
* l'état du RAID
* le nombre de périphériques actifs
* l'état de synchronisation

---

# 18. Sauvegarder la configuration RAID

Pour conserver la configuration du RAID :

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf

Puis :

cat /etc/mdadm.conf

---

# 19. Montage automatique au démarrage

Pour que le volume soit automatiquement monté après un redémarrage, il faut récupérer son UUID :

sudo blkid /dev/vgdata/lvdata

Exemple :

/dev/vgdata/lvdata: UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" TYPE="xfs"

Modifier ensuite `/etc/fstab` :

sudo nano /etc/fstab

Ajouter :

UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /mnt/raid1 xfs defaults 0 0

Tester la configuration :

sudo umount /mnt/raid1

Puis :

sudo mount -a

Et vérifier :

df -h /mnt/raid1

Si aucune erreur n'apparaît, le montage automatique fonctionne.

---

# 20. Tester l'état final

Commandes utiles :

lsblk

cat /proc/mdstat

sudo mdadm --detail /dev/md0

sudo pvs

sudo vgs

sudo lvs

df -h

---

# 🧠 Résumé

L'ordre de création est important :

1. Disques
      ↓
2. RAID 1
      ↓
3. /dev/md0
      ↓
4. Physical Volume (PV)
      ↓
5. Volume Group (VG)
      ↓
6. Logical Volume (LV)
      ↓
7. Système de fichiers XFS
      ↓
8. Montage
      ↓
9. /mnt/raid1

### Commandes principales

lsblk

sudo dnf install mdadm lvm2 -y

sudo mdadm --create --verbose /dev/md0 \
--level=1 \
--raid-devices=2 \
/dev/sdb /dev/sdc

cat /proc/mdstat

sudo mdadm --detail /dev/md0

sudo pvcreate /dev/md0

sudo vgcreate vgdata /dev/md0

sudo lvcreate -L 3G -n lvdata vgdata

sudo mkfs.xfs /dev/vgdata/lvdata

sudo mkdir -p /mnt/raid1

sudo mount /dev/vgdata/lvdata /mnt/raid1

df -h

## ✅ Résultat

Le lab permet de mettre en place une architecture :

**RAID 1 → LVM → XFS → Montage**

Le RAID 1 apporte la **redondance**, tandis que LVM apporte une **gestion flexible des volumes**.

