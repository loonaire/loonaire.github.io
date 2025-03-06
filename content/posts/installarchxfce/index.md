---
title: "Installation d'archlinux avec xfce"
date: 2025-03-06T10:39:35+01:00
draft: false
comment: false
---


> Ce fichier date de 2022 et n'a pas été mis à jour depuis, il n'est pas à jour et certaines pratiques utilisées ici ne sont à mon avis pas mes meilleures.
> A l'origine cet installation est destinée à l'installation sur un ordinateur MSI GP72 (cpu intel, cg nvidia, ssd 128go + hdd 1to)
> Prenez le tel quel si vous souhaitez l'utiliser

Démarrer sur l'iso, passer le clavier en fr et connecter le wifi
```sh
loadkeys fr
iwctl --passphrase=PASSPHRASE station DEVICE connect SSID # Ne pas utiliser --passphrase si ca ne fonctionne pas, il sera demandé ensuite
```

ensuite lancer archinstall

clavier fr, mirroirs fr

disques dur:
-> choisir le periph sur lequel on souhaite installer le système
-> Disque en btrfs pour un ssd puis reste par défaut

-> Pas de disk encryption

-> Chargeur démarrage: Systemd-boot

-> Swap True

-> Crée utilisateur et le mettre en sudo root

-> profil: desktop + xfce4 + all open source (A changer en fonction du matériel)

-> Audio: pipewire

-> Noyaux: linux 

-> Additionnal package: aucun (on les installera après)

-> Network : Utiliser NetworkManager

-> Fuseau horaire: Europe/Paris

-> Référentiel supplémentaire: multilib

après la fin: exit pour sortir du chroot puis reboot

-> install clean nvidia: pacman -S --needed nvidia-utils lib32-nvidia-utils nvidia-settings vulkan-icd-loader lib32-vulkan-icd-loader 
		PC portable: pacman -S --needed nvidia-prime power-profiles-daemon

Passer le tmp dans la ram:
ajouter au fstab:
tmp /tmp tmpfs defaults, noatime, mode=1777 0 0 

ajouter le disque dur au fstab:
UUID=partition /media/hdd ext4 defaults,rw,noatime 0 0


packet supplémentaire: papirus-icon-theme gufw git base-devel flatpak firefox onlyoffice calibre code gimp discord steam vlc wireshark hplip ttf-liberation noto-fonts noto-fonts-emoji noto-fonts-cjk neofetch go


instllation de yay pour l'accès à AUR et pamac:
-> Installation d'AUR (yay):
	-> aller sur https://github.com/Jguer/yay
````
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
```

-> Installer pamac-aur
```
yay -S pamac-aur --editmenu
```
Modifier pkgbuild de libpamac (editer avec vim):
````
ENABLE_FLATPAK=1
```

-> Mise en place du thème:
 	Dans AUR installer matcha-gtk-theme
	-> Passer le thème en matcha-dark-sea
	-> Icones papirus-dark
	-> polices noto sans regular
-> Paramètres Gestionnaire de fenetres:
	-> Style: Matcha-sea



Installation des paquets AUR:
yay -S code-marketplace onlyoffice 

-> Installer le bluetooth:
Installer: bluez bluez-tools bluez-utils blueman
	-> Démarrer automatiquement le bluetooth: systemctl enable bluetooth 

-> Activer le double clique lors du double tape sur le touchpad: dans paramètres -> Souris et pavé tactile, régler







