---
title: "Linux: Installer les lib en 32bits sur les architectures 64bits"
date: 2022-09-22T20:32:38+02:00
draft: false
comment: false
---

Ayant eu besoin d'utiliser le lecteur Flash Player 9 en StandAlone sur linux, j'ai dû apprendre un peu l'architecture des systèmes linux.  

Pour les personnes ayant l'habitude de Windows, dans la version 64bits il y a un mode de compatibilité pour les applications 32bits intégrée, Windows peux executer nativement les applications 32bits, mais les architectures 32 et 64bits sont différentes.

Sur les architectures 64bits, Linux installe seulement les versions 64bits des paquets, si on souhaite installer les versions 32bits il faut ajouter la possibilité d'installet les paquets 32bits.

###### Sur les distributions Archlinux et dérivées

Il faut activer les paquets AUR, les paquets seront nommés: lib32-nomPaquet

Sous Manjaro, les paquets AUR sont activables via le gestionnaire de paquet graphique.

Exemple pour la lib gtk2 sur Arch:

Il faut installer les paquets: 

+ lib32-gtk2

+ lib32-gtk2-engine-murrine

+ lib32-libcanberra

+ lib32-gnome-themes-extra

###### Sur les distributions Debian et ses dérivées

Il faut activer la prise en charge de l'architecture 32 bits (i386 sur Debian) avec dpkg. Les paquets seront instalables en utilisant la commande: **apt install nomPaquet:i386**    

Attention: la commande apt search ne renvoi pas l'information sur la disponibilité d'un paquet i386.

***Pour activer la prise en charge de l'architecture 32bits:***

```shell
dpkg --add-architecture i386
apt update
apt install nomlib:i386 

#pour gtk2 : 
apt install libgtk2.0-0:i386
```


