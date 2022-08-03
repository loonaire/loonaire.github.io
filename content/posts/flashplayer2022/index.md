---
title: "Utiliser Flash Player en 2022"
date: 2022-08-03T12:18:27+02:00
draft: false
comment: false
---

Flash player est aujourd'hui totalement mort, même adobe a retirer les liens vers Flash. Pour les applications AIR, une entreprise à repris le support, [plus d'infos par ici.](https://airsdk.harman.com/runtime)

Dans mon cas, je cherchais surtout sur les applications Flash. 

## **Lancer une application Flash player**

Lancer une application est plutot simple, il suffit de se munir du fichier swf de l'application ainsi que d'un lecteur flash player standalone.

Le lecteur flash player peux être trouver sur archive.org, par exemple [par ici.](https://archive.org/details/standaloneflashplayers)

> **Précision**  
> En fonction de l'application que vous souhaitez lancer, il peux y avoir besoin d'une version précise de flash (par exemple actionscript3 est supporté à partir du lecteur en version 9)

> **Pour linux**  
> Dans le cas de linux, il faut installer les paquets gtk2 en 32bits pour pouvoir lancer flash player, sous manjaro il s'agit du paquet lib32/gtk2


## **Autoriser une application Flash player à accéder au réseau**

A partir de flash player 9 il y a une sécurité qui empêche l'accès à internet d'une application. Pour autoriser une application, il faut aller dans le dossier utilisateur des paramètres du lecteur flash ( %appdata%/Macromedia pour windows, ~/.Macromedia pour linux), ensuite dans le dossier **Flash Player il** faut créer le dossier **#Security** puis dans ce dossier créer un dossier **FlashPlayerTrust**

Dans le dossier FlashPlayerTrust, il suffira d'ajouter un ou des fichiers avec l'extension .cfg qui contiendrons un répertoire qui autorisera les applications Flash.

Par exemple pour autoriser tout les dossiers sur l'ordinateur sur Windows:

**All.cfg:**
```
C:/
```

Ainsi, tous les swf pourront être lancer.


## **Utiliser le mode debug**

Pour activer le mode debug, il faut utiliser un flash player standalone debug, il peux être trouvé sur archive.org avec le lien plus haut.

Il faut ensuite crée un fichier mm.cfg qui contiendra la configuration nécessaire, ce fichier doit être dans votre répertoire utilisateur (%userprofile% pour windows ~/ pour linux).  
Voici le contenu de mon fichier:  
mm.cfg:
```
    AssetCacheSize=20
    AutoUpdateDisable=0
    AutoUpdateInterval=-1
    AVHardwareDisable=0
    DisableDeviceFontEnumeration=0
    DisableProductDownload=0
    ErrorReportingEnable=1
    FileDownloadDisable=0
    FileUploadDisable=0
    FullScreenDisable=0
    LocalFileReadDisable=0
    LocalStorageLimit=6
    MaxWarnings=1
    PolicyFileLog=1
    PolicyFileLogAppend=1
    SuppressDebuggerExceptionDialogs=0
    TraceOutputFileEnable=1
     TraceOutputFileName=C:\Users\Nom_utilisateur\AppData\Roaming\Macromedia\Flash Player\Logs\flashlog.txt
```

La variable MaxWarning est à modifier pour avoir plus ou moins de message Warning.  
Pour la sortie du debug, elle se trouvera dans le fichier flashlog.txt dans %appdata%/Macromedia\Flash Player\Logs ou ~/.Macromedia\Flash Player\Logs


