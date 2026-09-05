# 2. Simulation d'une panne du disque /dev/sdb

Avant de simuler la panne, on vérifie d'abord l'état du RAID :

```bash
lsblk
cat /proc/mdstat
sudo mdadm --detail /dev/md0
```

* `lsblk` : permet de voir les disques présents sur la machine.
* `cat /proc/mdstat` : permet de voir l'état du RAID.
* `sudo mdadm --detail /dev/md0` : donne plus de détails sur le RAID et sur l'état des disques.

### Simulation de la panne

On simule maintenant une panne du disque `/dev/sdb` :

```bash
sudo mdadm --manage /dev/md0 --fail /dev/sdb
```

Cette commande indique au RAID que `/dev/sdb` est en panne.

Le RAID 1 passe alors en **mode dégradé**. Le système continue de fonctionner grâce à `/dev/sdc`.

On vérifie l'état du RAID :

```bash
cat /proc/mdstat
sudo mdadm --detail /dev/md0
```

On doit voir que `/dev/sdb` est marqué comme **failed** et que `/dev/sdc` continue de fonctionner.

### Retrait du disque défaillant

On retire ensuite `/dev/sdb` du RAID :

```bash
sudo mdadm --manage /dev/md0 --remove /dev/sdb
```

Cette commande retire le disque en panne de la configuration du RAID.

On vérifie à nouveau :

```bash
cat /proc/mdstat
sudo mdadm --detail /dev/md0
```

Le RAID fonctionne maintenant avec seulement `/dev/sdc`.

### Vérification de LVM

On vérifie ensuite que la partie LVM fonctionne toujours :

```bash
sudo pvs
sudo vgs
sudo lvs
```

* `pvs` : affiche le disque utilisé par LVM.
* `vgs` : affiche le groupe de volumes `vgdata`.
* `lvs` : affiche le volume logique `lvdata`.

Enfin, on vérifie que les données sont toujours accessibles :

```bash
df -h /mnt/raid1
```

Cette commande permet de vérifier que `/mnt/raid1` est toujours disponible.

### Résultat

Même si `/dev/sdb` tombe en panne, les données restent disponibles grâce à `/dev/sdc`.

Le RAID 1 permet donc de continuer à utiliser les données même lorsqu'un des deux disques tombe en panne.

