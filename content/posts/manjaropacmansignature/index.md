---
title: "Manjaro: Corriger le problème de mise à jour des clés de signatures"
date: 2022-09-22T19:53:44+02:00
draft: false
comment: false
---

En réalisant une installation de Manjaro XFCE à partir d'un iso ayant quelques mois, je me suis retrouvé dans l'impossibilité de mettre à jour le système car les clés de signature des paquets n'étaient plus valide. Pour débloquer ces mises à jours, il faut recharger la liste des clés mises à jour.

Voici les commandes à saisir:

```shell
# Force la mise à jour de la base de données des paquets
sudo pacman -Syy  
# Charge la liste des clés la plus récent
sudo pacman-key --refresh-keys
# Indique quelles clés doivent être utilisées
sudo pacman-key --populate archlinux manjaro
# Vide le cache des paquets
sudo pacman -Sc
```

Il suffit ensuite de relancer une mise à jour des paquets.
