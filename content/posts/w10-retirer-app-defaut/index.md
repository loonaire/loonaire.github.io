---
title: "Windows 10: Retirer une application par défaut pour un format de fichier"
date: 2023-02-27T17:39:41+01:00
draft: false
comment: false
---

Sous Windows 10, il est impossible de retirer une application par défaut pour un type de fichier depuis les paramètres. Cette option est pourtant utilse et obligatoire pour éviter à certains logiciels de se mettre par défaut au lancement d'un fichier.


Pour retirer une association, il faut aller dans le registre de windows (regedit) et aller dans le dossier:
> HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts 

Ensuite, il suffit de supprimer le dossier de l'extension souhaitée. Enfin, pour que tous soit en ordre, il est préférable de redémarrer la session ou l'ordinateur pour prendre en compte les "nouveaux" paramètres.  
Si ce n'est pas possible, il faudra fermer et réouvrir les applications comme le gestionnaire de fichier pour que les modifications soient prises en compte.

[Source du forum Microsoft](https://answers.microsoft.com/fr-fr/windows/forum/all/supprimer-application-par-d%C3%A9faut-par-type-de/9edfbfc6-f83d-4d68-b2d0-4070101b0a1d)