---
title: "Télécharger un iso de Windows 11 pour n'importe quelle édition avec le media creation tools"
date: 2026-04-22T20:11:58+02:00
draft: false
comment: false
---

Le problème: l'obtention d'un iso de windows entreprise se faire obligatoirement via le portail visual studio, or si on n'a pas accès à ce portail ca peux être compliqué de l'obtenir.

## L'outil de création de media

Pour l'obtenir, on passe par l'outil mediacreationtool fourni pour télécharger l'iso de windows et crée des clés usb de windows. L'outil est disponible [ici](https://www.microsoft.com/fr-fr/software-download/windows11). La procédure classique est de simplement le lancer et suivre les instructions. Attention: il faut le lancer en étant **connecter avec un compte qui a les droits administrateurs locaux sur la machine**.

## La méthode

En étant connecté avec un compte administrateur local, on peux utiliser les arguments de la ligne de commande pour récupérer un iso spécifique.

Voici les arguments importants:
- MediaArch : l'architecture de processeur pour l'iso, pour intel il faut indiquer la valeur x64, pour arm indiquer arm64
- /MediaLangCode : la langue de l'iso, fr-FR pour la langue française, en-US pour l'anglais américain,...
- /MediaEdition : L'edition de windows souhaitée: Enterprise, Education, Home, ...


Pour l'édition enteprise pour processeur intel/amd 64bit il faut saisir la commande suivante:  

```sh
MediaCreationTool.exe /Eula Accept /Retail /MediaArch x64 /MediaLangCode fr-FR /MediaEdition Enterprise
```

Pour l'édition education:  
```sh
MediaCreationTool.exe /Eula Accept /Retail /MediaArch x64 /MediaLangCode fr-FR /MediaEdition Education
```

## Utiliser DISM pour vérifier un iso 

Pour vérifier si un iso contient la bonne édition il faut utiliser la commande suivante (avec l'iso monter sur un lecteur):
```sh
dism /Get-WimInfo /WimFile:X:\sources\install.esd
```

Pour un fichier wim:
```sh
dism /Get-WimInfo /WimFile:X:\sources\install.wim
```

S'affichera ensuite la liste des éditions disponible, l'index est important à avoir pour l'extraction d'une version pour la conversion de fichier esd vers wim.

## Convertir un install.esd en install.wim

Les chemins sont à adapter, en utilisant des antislash (\\) pour les chemins et en adaptant la valeur de l'index:
```sh
dism /Export-Image /SourceImageFile:install.esd /SourceIndex:1 /DestinationImageFile:install.wim /Compress:Max
```