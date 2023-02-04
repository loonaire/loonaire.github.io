---
title: "Mise à jour du firmware SSD"
date: 2023-02-04T09:13:29+01:00
draft: false
comment: false
---

En janvier 2023, des mises à jour concernant des problèmes sur les SSD samsung 9x0 et 9x0PRO ont été détectées, ça a été l'occasion de découvrir comment se fait la mise à jour des SSD. Sans surprise, il s'agit d'une manière similaire à celle des BIOS.  

Pour commencer, sous Windows 10, on peux voir la version du firmware du SSD à l'aide de la commande:
```sh
WMIC diskdrive Get Caption, firmwarerevision
```

on aura ensuite les SSD disponible leur version de firmware:
```powershell
PS C:\Windows\System32> WMIC diskdrive Get Caption, firmwarerevision  
Caption                FirmwareRevision  
Samsung SSD 980 1TB    3B4QFXO7  
Samsung SSD 980 500GB  3B4QFXO7  
```

> Attention: Certains modèles bas de gamme ne proposent pas de mise à jour pour les SSD, ca semble être le cas de Crucial avec les series BX500

>Sous linux, la mise à jour semble possible avec l'outil **fwupd**

## Le cas de samsung

Pour samsung, dans mon cas j'ai 2 moyens de le mettre à jour:
- Sous windows, avec le logiciel Samsung Magician, il téléchargera tous et fera l'installation automatiquement, idéal pour les débutants.
- Un moyen plus général, un fichier iso qui contient un linux minimal est disponible, il contient la dernière mise à jour du firmware du SSD. L'outil met à jour tous les SSD samsung reconnu. Cet iso démarre sur une clé Ventoy ce qui permet de ne pas avoir à flasher spécifiquement une clé pour cet iso.

Les mises à jours des SSD samsung sont disponibles ici: https://semiconductor.samsung.com/consumer-storage/support/tools/

