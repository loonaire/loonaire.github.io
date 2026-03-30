---
title: "Installer et configurer Xrdp sur linux"
date: 2026-03-30T20:56:45+02:00
draft: false
comment: false
---

Bon... Bien sur je préfèrerais un système de rdp sous wayland, plus moderne et surtout quasi obligatoire avec les environnement de bureau libres actuels.
Je voulais me faire un ordinateur sur lequel je pourrais prendre la main en rdp, sans avoir d'écran connecté. J'ai besoin d'outils récents et d'un système stable, j'ai choisi Ubuntu car je maitrise et c'est simple à installer mais Fedora est également très bien.  
Pour le rdp, j'ai testé avec Kubuntu mais krdp est encore trop récent, SDDM (l'interface de connexion) ne fonctionne pas avec et il y a des soucis de connexions avec le rdp de Windows.  
Donc c'est parti pour Xubuntu et xrdp.

## L'installation

Rien de difficile:

On met à jour la distribution:
```sh
sudo apt update
```
```sh
sudo apt upgrade
```

Maintenant que tout est à jour, on installe xrdp:
```sh
sudo apt install xrdp
```

On vérifie ensuite le status du service avec la commande:
```sh
systemctl status xrdp
```

## Configuration autour de xrdp

Ajouter le groupe ssl-cert à l'utilisateur xrdp pour autoriser la connexion sécurisée via certificats:
```sh
usermod -a -G ssl-cert xrdp
```

Le firewall: ici avec ufw installé nativement sur ubuntu:
```sh
sudo ufw allow from 192.168.0.0/16 to any port 3389
```

> Ici je met 192.168.0.0/16 car cette plage d'adresse est la plage privée utilisée par défaut, normalement elle est de 192.168.1.0/24 pour les box classiques

**Pour que la connexion fonctionne, l'utilisateur ne doit pas déja être connecté sur une session, sinon il doit se déconnecter, sous windows cela se traduit par une déconnexion juste après la saisie des identifiants**