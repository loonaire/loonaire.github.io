---
title: "Extraire le fichier apk d'une application Android"
date: 2023-08-27T10:31:54+02:00
draft: false
comment: false
---

Pour commencer, il faut:
- Un smartphone Android avec les options de développeur et le mode débogage activé (taper 7 fois sur le numéro de build dans les paramètres pour activer le mode développeur puis dans le mode développeur activer le mode débogage). 
- Un ordinateur avec android platform tools installé (disponible [ici](https://developer.android.com/tools/releases/platform-tools))


## Localiser l'application

Pour commencer, s'assurer sur le téléphone est reconnu par la commande suivante:
```sh
adb devices
```

Ensuite Pour afficher la liste des applications installées, il faut utiliser la commande:
```sh
adb shell pm list packages -f -3
```

Sous linux, pour affiner les recherches, il est possible d'utiliser grep, exemple avec une application de génération de mot de passe que j'ai développée:
```sh
adb shell pm list packages -f -3 | grep *com.loonaire.*
```

Sous Windows, la commande est la suivante:
```sh
adb shell pm list packages -f -3 | findstr com.loonaire
```

Le résultat sera:
> package:/data/app/~~1q6hQk2QhK8pr2btHKVv6Q==/com.loonaire.GoPasswordGenerator-Y4kXay1AOFJ4PL4jxJRczA==/base.apk=com.loonaire.GoPasswordGenerator

## Récupérer l'APK

Pour récupérer l'apk, il faut utiliser le squelette de commande suivant:
```sh
adb pull /data/app/cheminVersLApp/base.apk nomDuFichierExtrait.apk
```

Voici la commande de mon exemple:
```sh
adb pull /data/app/~~1q6hQk2QhK8pr2btHKVv6Q==/com.loonaire.GoPasswordGenerator-Y4kXay1AOFJ4PL4jxJRczA==/base.apk passwordGenerator.apk
```

L'apk pourra être réinstaller sur un autre appareil.  
Pour installer l'apk depuis adb:
```sh
adb install nomFichier.apk
```


## Extraire les données d'application

> Attention: Ce système est dépréciée depuis Android 13 et sera retiré un jour

Pour extraire l'apk et les données de l'application:
```sh
adb backup -apk nomApp -f nomBackup.adb
```

Pour suivre mon exemple:
```sh
adb backup -apk com.loonaire.GoPasswordGenerator -f passwordGenerator.adb
```

Il faut ensuite choisir de sauvegarder les données sur le téléphones.

Pour restaurer une application:
```sh
adb restore "nom du fichier backup"
```

## Source

[https://gist.github.com/AnatomicJC/e773dd55ae60ab0b2d6dd2351eb977c1](https://gist.github.com/AnatomicJC/e773dd55ae60ab0b2d6dd2351eb977c1)





