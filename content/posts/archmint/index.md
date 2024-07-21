---
title: "Archlinux cinnamon: Avoir le thème de linux Mint"
date: 2024-07-21T07:56:24+02:00
draft: false
comment: false
---

## Installation

Un petit tuto pour avoir le thème de linux Mint sur archlinux:
- Tout d'abord, installer Archlinux normalement avec l'environ de bureau Gnome et choisir lightdm comme interface pour le login.
- Installer les paquets de traduction de cinnamon: ``pacman -S cinnamon-translations``
- Installer les polices d'écritures: ``pacman -S notofonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra``

- Ajouter l'accès à AUR via Yay ou un autre moyen
- Installer les paquets AUR suivants (la commande suivant est pour AUR): ``yay -S mint-themes mint-y-icons mint-x-icons``

- Configurer les thèmes: en allant dans l'outil themes :
    - Application: Mint-y-dark-orange 
    - Icones: Mint-Y-Aqua  
    - Bureau: Mint-Y-Dark
    - Dans l'onglet paramètres, autoriser le mode sombre pour les appli


- Avoir le format de l'heure comme sur Windows: 
     -Clique droit sur l'heure, activer l'heure personnalisé et saisir la chaine suivante: (prend tout ce qui est entre " ", y compris les espaces): <pre>"      %H:%M %n %d/%m/%Y"</pre>

## Liens utiles

- [https://wiki.archlinux.org/title/cinnamon](https://wiki.archlinux.org/title/cinnamon)
- [https://cinnamon-spices.linuxmint.com/applets/view/281](https://cinnamon-spices.linuxmint.com/applets/view/281)
- [https://github.com/David-H-Dev/LMAE](https://github.com/David-H-Dev/LMAE)