---
title: "Asus PCE AC68 sur linux"
date: 2026-03-27T10:49:57+01:00
draft: false
comment: false
---

En voulant passer un ordinateur sur linux j'ai eu une galère avec la carte wifi interne PCI Asus PCE-AC68.
La version USB de la carte est plus documentée et des drivers sont disponible, cependant pour la version PCI il n'y a rien, aucun driver pour linux. Même pour windows les drivers sont difficiles à trouver.
Les drivers broadcom sont compliqués à gérer par linux, il n'y a pas de version opensource de ces drivers disponible.

Pour Debian et les dérivées, il y a une solution, pour les autres distribution ca doit être plus ou moins la même chose:

- Première solution avec un accès internet:
```sh
apt update
apt install broadcom-sta-common broadcom-sta-dkms
```

redémarrer:
```sh
reboot
```

- Deuxième solution en construisant manuellement le paquet (le paquet peux être construit depuis une autre distribution ou avec une connexion internet):  

Sous Debian (en tant que root, sinon utiliser sudo):
```sh
apt update
apt install broadcom-sta-source module-assistant
```

> Le paquet module-assistant est déja installé sur ubuntu, cependant pour debian il n'est apparement pas installé et il installe en même temps les dépendances de compilation

```sh
m-a prepare

m-a build broadcom-sta 
```

il faut ensuite installer le paquet généré par la commande précédente:
```sh
dpkg -i /usr/src/<nom du paquet compilé>.deb
```

Puis redémarrer:
```sh
reboot
```

## Sources

 [https://www.reddit.com/r/debian/comments/9nlpsw/help_installing_wifi_device_drivers_asus_pceac68/?tl=fr](https://www.reddit.com/r/debian/comments/9nlpsw/help_installing_wifi_device_drivers_asus_pceac68/?tl=fr)