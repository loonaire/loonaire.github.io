---
title: "Créer ou dumper un iso sur cd/dvd sur Linux"
date: 2022-10-23T12:06:18+02:00
draft: false
comment: false
---

Je me suis longtemps demander comment dumper un fichier iso d'un cd ou dvd ou créer ce même fichier iso.
Sous Windows, il existe le logiciel imgburn qui permet d'extraire des isos ou d'en créer simplement, je souhaite aller un peu plus loin ici, surtout en le faisant manuellement, sous linux il suffit d'une commande pour extraire des fichiers iso.


## 0. Prérequis

Pour la suite du tutoriel, il faut installer le paquet qui contiendra les commandes que je vais utiliser:  
- Pour Debian et dérivées ainsi que RedHat, il faut le paquet **genisoimage**
- Pour Archlinux et MacOs, il faut le paquet **cdrtools**

## 1. L'extraction d'un fichier iso

Pour extraire un fichier iso depuis un lecteur, il faut utiliser la commande dd. Afin d'éviter que le fichier fasse la taille d'un dvd ou d'un cd entier (4.7Go ou 700Mo), le fichier résultant fera la taille utilisée sur le cd ou dvd.  
Il faudra donc utiliser les arguments bs (taille des blocks) et count (taille utilisée sur le support) de la commande dd. La commande isoinfo nous permet de récupérer ces informations avec les commandes:
```sh
isoinfo -d -i /dev/sr1 | grep -i -E 'volume size' # récupère la taille utilisée sur le volume
isoinfo -d -i /dev/sr1 | grep -i -E 'block size' # récupère la taille d'un bloc du volume
```

On peux ensuite faire l'extraction avec la commande dd:  
Le lecteur source peux être /dev/cdrom ou /dev/dvdrom ou /dev/srX, dans mon cas, il s'agit de /dev/sr0
Les valeurs de bs et de count sont à modifier en récupèrant les résultats des commandes précédentes

```sh
dd if=/dev/sr0 of=nomFichierExtrait.iso bs=2048 count=9999999 status=progress
```

Pour les DVD vidéo, une alternative est possible avec genisoimage:
```sh
genisoimage -dvd-video -udf -o nomIso.iso /dev/sr0
```

Enfin, on peux vérifier avec md5sum que le dump est correct:

```
md5sum dumpiso.iso 
md5sum /dev/sr0
```


Il est ensuite possible de monter l'iso avec la commande:
```sh
mount -o loop fichier.iso /mnt
```

pour le démonter:
```sh
umount /mnt
```


## 2. Créer un fichier iso

Cette méthode semble également marcher sur Windows, il faut l'outil / la commande **genisoimage** (pour windows / Debian / RedHat) ou **mkisofs** (Arch / MacOs), genisoimage est un fork de mkisofs donc les outils sont probablement quasiment identique, dans mon cas, sur Manjaro les 2 commandes étaient disponible.

Il faut un dossier qui contient tout ce qui sera dans le fichier iso.

```sh
genisoimage -o nomFichier.iso dossierQuiDeviendraIso
```

