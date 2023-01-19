---
title: "Extraire un fichier d'un fichier .exe qui contient un installateur et créer un jdk java 8 portable"
date: 2023-01-19T15:49:17+01:00
draft: false
comment: false
---

Dans l'optique de pouvoir lancer des logiciels qui ne se lancent que avec Java 8, j'ai cherché une version portable de java 8 or cela n'existe pas. En cherchant un peu j'ai trouvé un moyen qui permet de pouvoir créer une version "portable" de logiciels à partir du fichier .exe d'intallation. Cette technique permet également d'extraire les drivers des fcihiers exe d'installation si l'on souhaite installer seulement le driver.

## Extraire des fichiers d'un installateur .exe

> Attention: Il faut absolument utiliser un installateur "offline" qui contient déja les fichiers qu'il va installer, avec un installateur qui téléhcharge les fichiers, cette technique est inutile.

Il suffit d'ouvrir le fichier avec 7zip ou un autre outil d'archive. En extrayant le contenu du fichier .exe on se retrouve avec le contenu de l'installateur, pour certains logiciels (l'installateur des pilotes AMD par exemple) les fichiers seront directement disponible, pour d'autres installateurs il s'agira de fichier incompréhensible et il faudra extraire en boucle les fichiers pour trouver ce qui est intéressant (par exemple c'est le cas des drivers Nvidia et de l'installateur de Java JDK 8).  

## Le cas de Java 8

Java 8 est un cas particulier, les fichiers contenu dans l'installateur sont nommés suivant des segments assembleur. Pour obtenir le contenu installer par l'installateur, il faut aller dans le dossier ``` jdk-8u341-windows-x64\.rsrc\1033\JAVA_CAB10 ``` et extraire le fichier ``` 111 ```. Dans ce fichier se trouve un fichier tools.zip qui contient en réalité le JDK de java 8.  
Après avoir extrait les fichier de l'archive tools.zip, il faut aller dans le dossier avec une invite de commande, puis taper la commande (si elle ne marche pas, aller directement dans /bin et executer le fichier java.exe à l'aide d'une invite de commande):  
```batch
bin/java.exe -version
```
Normalement le message suivant s'affichera:
```
Error occurred during initialization of VM
java/lang/NoClassDefFoundError: java/lang/Object
```

Pour résoudre se problème, il faut se placer dans le dossier qui contient les fichiers du jdk puis éxecuter la commande:
```batch
for /r %x in (*.pack) do .\bin\unpack200 -r "%x" "%~dx%~px%~nx.jar"
```

Enfin normalement cette fois en répétant la commande cela devrais marcher:
```batch
bin/java.exe -version
```
Résultat:
```
java version "1.8.0_341"
Java(TM) SE Runtime Environment (build 1.8.0_341-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.341-b10, mixed mode)
```

Le dossier tools extrait peu désormais servir de JDK Java 8 portable. Il est également possible de l'ajouter aux variable d'environnement pour pouvoir l'appeler directement depuis l'invite de commande.