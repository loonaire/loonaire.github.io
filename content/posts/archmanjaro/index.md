---
title: "Installer pamac et le thème Manjaro sur Archlinux"
date: 2024-07-21T08:15:42+02:00
draft: false
comment: false
---

> Avertissement: Ces notes ont déja 2 ans et risquent de ne plus être à jour

## Prérequis

Avoir un Archlinux installé avec l'environnement de bureau XFCE

## Installation de pamac

- Avoir un moyen d'installer les paquets des dépots AUR, dans mon cas je privilégie Yay: [https://github.com/Jguer/yay](https://github.com/Jguer/yay)
- Installer Pamac:
    - Executer la commande: ``yay install pamac-aur --edit-menu``
    - Choisir libpamac-aur et pamac-aur
    - Pour activer les SNAP et/ou les FLATPAK, éditer le fichier PKGBUILD ainsi:
        - Pour SNAP: ENABLE_SNAP=1
        - Pour FLATPAK: ENABLE_FLATPAK=1

## Installation du thème

- Installer le paquet nono-fonts et mêttre cette police par défaut
- Installer les icones, le paquet est nommé ``papirus-icon-theme``
    - Dans apparence -> Icones -> choisir papirus-Light ou papirus-Dark
- Installer le thème depuis les dépots AUR, le paquet est nommé 
``matcha-gtk-theme``
    - Dans Apparence -> Style -> choisir Matcha-dark-sea