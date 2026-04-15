---
title: "Réparer systemdboot sur Archlinux"
date: 2025-03-06T09:59:08+01:00
draft: false
comment: false
---

## Prérequis

Démarrer l'ordinateur avec un iso archlinux

## Préparer le chroot:

- Obtenir la liste des partitions avec la commande:
```sh
lsblk
```

Il faut la partition qui contient le système ainsi que la partition qui contient les fichiers de boot
- Monter ces 2 partitions (ici j'utilise sda comme disque ou est installé le système): 
```sh
mount /dev/sdaX /mnt # system partition
mount /dev/sdaY /mnt/boot # boot partition
```

- On peux maintenant chroot:
```sh
arch-chroot /mnt
```

Nous sommes maintenant dans le chroot et nous pouvons intéragir sur le système installer comme si il était installé normalement

## Réparer le boot de systemdboot

### Systemd < 258
Il faut lancer la commande:
```sh
bootctl --path=/boot install
```

### Systemd > 258
A partir de systemd 258 il y a un problème avec la commande bootctl et archlinux.
Il faut lancer la commande:
```sh
efibootmgr --create --disk /dev/sdX --part Y --loader '\EFI\systemd\systemd-bootx64.efi' --label "Linux Boot Manager" --unicode
```
Où:
- ``sdX`` le X correspond à la lettre du disque qui contient la partition /boot
- ``Y`` est la partition qui contient ``/boot``


Ensuite un message nous indique qu'une nouvelle entrée de démarrage a été crée, elle apparais désormais dans notre bios (si elle n'apparassait plus)
> Created EFI boot entry "Linux Boot Manager"

Nous pouvons ensuite sortir du chroot:

```sh
exit # Sortie du chroot
unmount -R /mnt # Démonte les partitions montées
reboot
```

Au moment du reboot, remettre le disque qui contient le système en premier dans le bios ou retirer la clé ou le cd d'archlinux.
Notre Archlinux est réparé.

## L'entrée est valide mais le kernel n'est pas disponible

Démarrer sur l'iso, passer le clavier en fr et connecter le wifi
```sh
loadkeys fr
iwctl --passphrase=PASSPHRASE station DEVICE connect SSID # Ne pas utiliser --passphrase si ca ne fonctionne pas, il sera demandé ensuite
```

- monter les partitions /boot et / (comme indiquer au dessus)

- lancer une mise à jour avec pacman -Syyuu

- quitter le chroot et redémarrer

## Sources

[https://www.jr-it-services.de/restoring-a-linux-systemd-boot-loader-on-a-dual-boot-machine-after-a-windows-11-update/](https://www.jr-it-services.de/restoring-a-linux-systemd-boot-loader-on-a-dual-boot-machine-after-a-windows-11-update/)
