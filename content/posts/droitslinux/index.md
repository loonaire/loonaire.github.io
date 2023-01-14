---
title: "Les droits de fichiers et répertoire linux"
date: 2022-10-28T10:58:29+02:00
draft: false
comment: false
---

## Les droits

### Les droits de base

|Lettre| Valeur en Octal | Nom du mode |Effet sur un dossier| Effet sur fichier|
|:----:|:---------------:|:-----------:|:------------------:|:-----------------:|
|x|1|Execution|Permet de se déplacer dans un dossier|Permet de rendre executable le fichier, utiliser seul ne permet rien|
|w|2|Ecriture|Permet la création de fichier et dossier|Autorise la modification du fichier|
|r|4|Lecture|Permet de voir le contenu d'un dossier|permet de lire le contenu du fichier, ce droit doit être présent pour permettre l'éxecution du fichier|

### Les droits spéciaux

Ces droits donnent des droits supplémentaires, dans le cas lors de l'utilisation de la commande ls -l, le x sera remplacé par un autre caractère.  

|Lettre|Valeur en Octal|Nom du droit spécial|Position du droit spécial|Effet|
|:----:|:-------------:|:------------------:|:-----------------------:|:---:|
|t|1|sticky bit|Utilisateur|Droit qui se place sur un dossier, empêche les autres utilisateur de supprimer les fichiers de quelqu'un d'autre|
|s|2|SGID|Groupe propriétaire|Même chose que SUID mais au niveau du groupe propriétaire|
|s|4|SUID|Tout le monde|S'applique à un fichier executable, execute le fichier en tant que utilisateur propriétaire du fichier|



## Remarques sur la commande chmod

Une bonne pratique sur la commande chmod est lors de la modification des droits, la commande ```sh chmod 755``` ou ```sh chmod 777``` est une mauvaise pratique, il faut préciser le sticky bit pour éviter de se retrouver avec n'importe quel utilisateur qui peux faire n'importe quoi, un exemple de commande serait plutot ```sh chmod 1777```

## Liens

[wikipedia permissions unix](https://fr.wikipedia.org/wiki/Permissions_UNIX)  
[Wikipedia chmod](https://fr.wikipedia.org/wiki/Chmod)  
[cours permissions linux](https://linux.goffinet.org/administration/securite-locale/permissions-linux/)  
[Petit article sur les permissions](https://www.cyberciti.biz/faq/howto-set-readonly-file-permission-in-linux-unix/)  

